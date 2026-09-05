---
name: gpu-infra-provisioning
description: >
  Provision GPU worker nodes across 17 cloud providers via Terradev and commit
  k0smotron RemoteMachine manifests to a Flux-managed GitOps repository.
  Use when users need to add GPU compute to a Flux-managed cluster, want to
  provision spot or on-demand GPU instances, or need to generate and commit
  RemoteMachine manifests for k0smotron Anywhere.
license: Apache-2.0
allowed-tools: Bash, Read, Write, Edit, Glob, Grep
---

# GPU Infrastructure Provisioning

You are an expert in provisioning GPU compute for Flux-managed Kubernetes clusters
using Terradev. You provision instances across cloud providers, generate k0smotron
`RemoteMachine` manifests, and commit them to a GitOps repository so Flux can
reconcile the new worker nodes automatically.

**Rules:**
- Always verify Terradev is installed before provisioning: `terradev --version`
- Check provider availability before committing to a cost: `terradev providers list --gpu <type>`
- Never hard-code provider API keys — read them from environment variables or
  the Terradev credential store (`~/.terradev/credentials.json`)
- After generating manifests, validate YAML structure before committing
- Always use the `terradev k8s node add` command when a knr-ops-style GitOps
  repo is the target — it handles kustomization patching and state tracking
- Load `references/providers.md` for the full provider list, GPU availability,
  and pricing guidance before recommending a provider

## Workflow

### Phase 1 — Assess

1. Ask the user: target GPU type (H100, A100, RTX4090, L40S, ...), count,
   maximum price per hour, spot vs on-demand, and the GitOps repo path.
2. Run `terradev providers list --gpu <type> --format table` to show current
   availability and spot prices across all configured providers.
3. If no providers are configured, guide the user through
   `terradev configure --provider <name>` for their preferred provider.

### Phase 2 — Provision

4. Provision with `terradev k8s node add <node-id> --gpu <type> --provider <name>
   --repo <gitops-repo-path>`. For spot instances add `--spot`.
5. If the provider has not yet assigned an IP, the command exits with a pending
   hint. Once the IP is available, run `terradev k8s node ready <node-id>
   --address <ip> --provider <name> --gpu <type> --repo <gitops-repo-path>`.
6. Verify the manifests were committed:
   `terradev k8s node list --repo <gitops-repo-path>`

### Phase 3 — Reconcile

7. Push the GitOps repo: `git -C <repo> push origin main`
8. Watch Flux reconcile the new worker:
   ```
   flux get kustomizations -w
   kubectl get machines -n default -w
   ```
9. Confirm the node joined the cluster:
   `kubectl get nodes -l terradev.io/gpu-type=<type>`

### Phase 4 — Clean up

10. To remove a node: `terradev k8s node rm <node-id> --repo <gitops-repo-path>`
    then push the repo. The node will be removed from the k0smotron control plane
    and the underlying instance terminated.

## Edge Cases

- **IP not assigned within 5 minutes**: Most providers assign IPs within 60 seconds.
  If not, check the provider console and use `node ready` once available.
- **SSH key not found**: The manifest references a `gpu-ssh-key` Kubernetes Secret.
  Ensure it exists in the target namespace before pushing.
- **Multiple providers configured**: `node add` without `--provider` picks the
  cheapest matching instance. Pass `--provider` to force a specific one.
- **Spot instance eviction**: Spot instances can be reclaimed. Use on-demand
  (`--spot` omitted) for long-running training jobs.
