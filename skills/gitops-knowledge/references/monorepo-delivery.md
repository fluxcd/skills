# Monorepo Delivery Reference

Generate the delivery pipeline for every application directory in a monorepo automatically.
For platform teams with hundreds of apps and environments in one repository, hand-writing a
Flux `Kustomization` per directory is constant churn: every app added, renamed or retired means
editing the Flux configuration too. With this pattern the pipelines follow the directory
structure — adding a directory deploys the app, removing it tears the deployment down.

**Contents:** [How It Works](#how-it-works) | [Prerequisites](#prerequisites) | [Repository Layout](#repository-layout) | [Manifests](#manifests) | [Day-2 Operations](#day-2-operations) | [Environment Segregation](#environment-segregation) | [Multi-Tenancy](#multi-tenancy) | [Operations](#operations)

## How It Works

Four objects, each reacting to the previous one:

```
GitRepository (monorepo)
  │
  ▼ artifact
ArtifactGenerator (pathPattern: "@monorepo/apps/{app}/envs/{env}")
  │
  ▼ one ExternalArtifact per matched directory, labeled app=…, env=…
ResourceSetInputProvider (type: ExternalArtifact, selectors by label)
  │
  ▼ one input set per artifact: id, name, namespace, revision + all labels
ResourceSet
  │
  ▼ one Flux Kustomization per input (sourceRef.kind: ExternalArtifact)
App workloads
```

1. A `GitRepository` pulls the monorepo into the cluster.
2. An `ArtifactGenerator` (from the `source-watcher` component) scans the repository artifact
   for directories matching `spec.pathPattern` and generates one `ExternalArtifact` per match.
   The named captures (`{app}`, `{env}`) become labels on the generated artifact.
3. A `ResourceSetInputProvider` of `type: ExternalArtifact` discovers the artifacts with label
   selectors and exports one input set per artifact — `id`, `name`, `namespace`, `revision`
   plus every label, so `app` and `env` are available as template variables.
4. A `ResourceSet` templates a Flux `Kustomization` per input, pointing at the artifact.

The pipeline is event-driven: the operator watches `ExternalArtifact` objects and reacts as soon
as they are created, updated or deleted — no polling interval to tune on the provider.

## Prerequisites

Flux **v2.9.0 or later** with the `source-watcher` component enabled on the `FluxInstance`:

```yaml
apiVersion: fluxcd.controlplane.io/v1
kind: FluxInstance
metadata:
  name: flux
  namespace: flux-system
spec:
  distribution:
    version: "2.9.x"
    registry: "ghcr.io/fluxcd"
  components:
    - source-controller
    - kustomize-controller
    - helm-controller
    - notification-controller
    - source-watcher
```

## Repository Layout

Each app has a Kustomize base and one overlay per environment:

```text
platform-monorepo/
└── apps/
    ├── auth/
    │   ├── base/
    │   │   ├── kustomization.yaml
    │   │   └── deployment.yaml
    │   └── envs/
    │       ├── dev/
    │       │   └── kustomization.yaml
    │       └── prod/
    │           └── kustomization.yaml
    └── payments/
        ├── base/
        └── envs/
            ├── dev/
            └── prod/
```

Overlays reference the base with a relative path, which is why the generator must copy the
base alongside the overlay (see below):

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
  - ../../base
patches:
  - target:
      kind: Deployment
    patch: |
      - op: replace
        path: /spec/replicas
        value: 2
```

## Manifests

All four objects live in the same namespace (here `apps`), because the provider discovers
artifacts in its own namespace by default and the generated Kustomizations deploy into it.

### 1. GitRepository

```yaml
apiVersion: source.toolkit.fluxcd.io/v1
kind: GitRepository
metadata:
  name: platform-monorepo
  namespace: apps
spec:
  interval: 5m
  url: https://github.com/org/platform-monorepo
  ref:
    branch: main
  # secretRef: { name: git-credentials }   # private repos
```

On production clusters follow semver tags instead of a branch (`ref.semver: ">=1.0.0"`) so
changes roll out only when the monorepo is tagged.

### 2. ArtifactGenerator

```yaml
apiVersion: source.extensions.fluxcd.io/v1beta1
kind: ArtifactGenerator
metadata:
  name: platform-apps
  namespace: apps
spec:
  sources:
    - alias: monorepo
      kind: GitRepository
      name: platform-monorepo
  commonMetadata:
    labels:
      team: platform                       # extra label the provider can select on
  pathPattern: "@monorepo/apps/{app}/envs/{env}"
  artifacts:
    - name: "{app}-{env}"                  # auth-dev, auth-prod, payments-dev, payments-prod
      copy:
        - from: "@monorepo/apps/{app}/base/**"
          to: "@artifact/base/"
        - from: "@monorepo/apps/{app}/envs/{env}/**"
          to: "@artifact/envs/{env}/"
```

- `{app}` and `{env}` act as wildcards when matching directories; their captured values
  template the artifact name and are set as **labels** on each `ExternalArtifact`
  (`app: auth`, `env: dev`), alongside `commonMetadata` labels.
- The two `copy` operations **replicate the app directory structure inside the artifact**
  (`base/` + `envs/<env>/`) so the overlay's `../../base` reference resolves. Copying only the
  overlay directory is the most common mistake — the Kustomization build then fails on the
  missing base.
- Each artifact contains only its app's base and one overlay, so its revision changes only when
  those directories change in Git — unrelated apps do not reconcile.

Generated artifact (for reference):

```yaml
apiVersion: source.toolkit.fluxcd.io/v1
kind: ExternalArtifact
metadata:
  name: auth-dev
  namespace: apps
  labels:
    app: auth                # from {app}
    env: dev                 # from {env}
    team: platform           # from commonMetadata
    app.kubernetes.io/managed-by: source-watcher
status:
  artifact:
    revision: latest@sha256:6e7d...
```

### 3. ResourceSetInputProvider

Select only the artifacts for this cluster's environment:

```yaml
apiVersion: fluxcd.controlplane.io/v1
kind: ResourceSetInputProvider
metadata:
  name: platform-apps
  namespace: apps
spec:
  type: ExternalArtifact
  selectors:
    - matchLabels:
        team: platform
        env: dev
```

No `url` for this type. Discovery is scoped to the provider's namespace by default, and the
operator also watches `ExternalArtifact` objects, so changes are picked up immediately rather
than waiting for the `reconcileEvery` interval. Exported inputs:

```yaml
status:
  exportedInputs:
    - id: "592053506"
      name: auth-dev
      namespace: apps
      revision: latest@sha256:6e7d...
      app: auth
      env: dev
      team: platform
    - id: "1023018689"
      name: payments-dev
      namespace: apps
      revision: latest@sha256:9a8b...
      app: payments
      env: dev
      team: platform
```

Selector variants: `name:` to pick one artifact, `matchExpressions` for set logic, and
`namespace: <ns>` or `namespace: "*"` to discover outside the provider's namespace (the
`"*"` form needs cluster-wide `list` on ExternalArtifacts — see [Multi-Tenancy](#multi-tenancy)).
The provider exports at most `spec.filter.limit` inputs (default 100); raise it for larger
monorepos.

### 4. ResourceSet

```yaml
apiVersion: fluxcd.controlplane.io/v1
kind: ResourceSet
metadata:
  name: platform-apps
  namespace: apps
spec:
  inputsFrom:
    - kind: ResourceSetInputProvider
      name: platform-apps
  resources:
    - apiVersion: kustomize.toolkit.fluxcd.io/v1
      kind: Kustomization
      metadata:
        name: << inputs.app >>
        namespace: << inputs.provider.namespace >>
      spec:
        interval: 30m
        retryInterval: 5m
        timeout: 5m
        prune: true
        wait: true
        sourceRef:
          kind: ExternalArtifact
          name: << inputs.name >>
        path: ./envs/<< inputs.env >>
        targetNamespace: << inputs.provider.namespace >>
```

Each generated Kustomization points at its artifact and builds the environment overlay from
the artifact contents. On the dev cluster this yields `auth` and `payments` Kustomizations in
`apps`, sourced from `auth-dev` and `payments-dev`. Name the Kustomization after
`inputs.app` (not `inputs.name`) so the object name stays stable across environments.

## Day-2 Operations

- **Adding an app or environment** — push a directory matching the pattern; an
  `ExternalArtifact` appears, the provider on the matching cluster exports a new input, the
  ResourceSet creates the Kustomization.
- **Changing manifests** — the affected artifact gets a new revision; only that app's
  Kustomization reconciles.
- **Removing an app or environment** — delete the directory; the artifact is removed, the
  input disappears, the ResourceSet deletes the Kustomization, which prunes the workloads.

## Environment Segregation

The same `GitRepository`, `ArtifactGenerator` and `ResourceSet` apply to every cluster; only
the provider's `env` selector differs. Avoid per-cluster provider variants by substituting
the value from a per-cluster ConfigMap through the Flux Kustomization that applies these
manifests:

```yaml
spec:
  type: ExternalArtifact
  selectors:
    - matchLabels:
        team: platform
        env: ${CLUSTER_ENV}      # substituted via postBuild.substituteFrom
```

On production, restrict *when* changes roll out by attaching deployment windows to the
provider with `spec.schedule` (cron + `timeZone` + `window`).

## Multi-Tenancy

By default the operator lists `ExternalArtifact` objects with its own service account, which
has cluster-wide read access. To restrict discovery to a tenant's permissions, set
`spec.serviceAccountName` on the provider; when the operator runs with
`--default-service-account`, impersonation is enforced for all providers. With
`namespace: "*"` selectors, the impersonated account must hold cluster-wide `list` on
ExternalArtifacts or reconciliation fails with a forbidden error. Pair this with
`spec.serviceAccountName` on the ResourceSet so the generated Kustomizations run under the
same tenant identity.

## Operations

```shell
flux operator -n apps get all                       # ResourceSet, provider, generated Kustomizations
kubectl -n apps get rsip platform-apps -o yaml       # inspect exported inputs
flux operator -n apps reconcile rsip platform-apps   # force artifact discovery now
flux operator -n apps suspend rset platform-apps     # pause generation
flux operator -n apps resume rset platform-apps

# Render locally with mock inputs before applying
flux operator build rset -f platform-apps-resourceset.yaml \
  --inputs-from-provider static-inputs.yaml
```

For ArtifactGenerator copy semantics and strategies see `references/sources.md`; for
ResourceSet templating, `Permute`, `dependsOn` and steps see `references/resourcesets.md`.
