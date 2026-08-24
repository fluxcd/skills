# gitops-knowledge

## v0.3.0 (2026-08-24)

The `gitops-knowledge` skill gains the Flux Operator ResourceSet features for monorepo delivery and staged Jobs: the `ExternalArtifact` input provider, step-based reconciliation with Job lifecycle rules, the directory-driven gitless fleet layout (`references/monorepo-delivery.md`), and post-build substitution pitfalls. The suite grows from 121 to 149 assertions with three new evals: `monorepo-delivery`, `staged-jobs`, and `fleet-scaffold` (scaffolds a two-cluster gitless monorepo from scratch and is graded by building every overlay with kustomize and schema-validating the output). One run per eval per configuration; evals 1-8 graded per-assertion by `claude-opus-5`, evals 9-14 graded programmatically. The no-skill baseline had the same tools available (Flux CLI, schema catalog MCP, web) and in several runs compensated by fetching upstream docs, which is reflected in its duration and token costs.

Model: `claude-fable-5`

**Results**

| Eval | With Skill | Baseline | Delta |
|------|-----------|----------|-------|
| OCI Helm chart | 11/11 (100%) | 9/11 (82%) | +18% |
| ResourceSet preview envs | 15/15 (100%) | 15/15 (100%) | 0% |
| Notifications | 16/16 (100%) | 13/16 (81%) | +19% |
| Image automation | 9/9 (100%) | 9/9 (100%) | 0% |
| FluxInstance + ResourceSets | 15/15 (100%) | 14/15 (93%) | +7% |
| Terraform bootstrap | 12/12 (100%) | 12/12 (100%) | 0% |
| Gitless publish pipeline | 14/14 (100%) | 14/14 (100%) | 0% |
| Air-gapped 2.9 fields | 11/11 (100%) | 10/11 (91%) | +9% |
| Add app in repo | 7/7 (100%) | 7/7 (100%) | 0% |
| Debug broken overlay | 5/5 (100%) | 5/5 (100%) | 0% |
| ResourceSet local render | 6/6 (100%) | 6/6 (100%) | 0% |
| Monorepo delivery | 9/9 (100%) | 9/9 (100%) | 0% |
| Staged Jobs | 9/9 (100%) | 9/9 (100%) | 0% |
| Fleet scaffold | 10/10 (100%) | 2/10 (20%) | +80% |
| **Overall** | **149/149 (100%)** | **134/149 (90%)** | **+10%** |

**Costs**

| Metric | With Skill | Baseline |
|--------|-----------|----------|
| Mean duration | 175s | 167s |
| Mean tokens | 64.2k | 44.5k |

---

Model: `claude-opus-5`

**Results**

| Eval | With Skill | Baseline | Delta |
|------|-----------|----------|-------|
| OCI Helm chart | 11/11 (100%) | 9/11 (82%) | +18% |
| ResourceSet preview envs | 15/15 (100%) | 13/15 (87%) | +13% |
| Notifications | 16/16 (100%) | 15/16 (94%) | +6% |
| Image automation | 9/9 (100%) | 9/9 (100%) | 0% |
| FluxInstance + ResourceSets | 15/15 (100%) | 14/15 (93%) | +7% |
| Terraform bootstrap | 12/12 (100%) | 6/12 (50%) | +50% |
| Gitless publish pipeline | 14/14 (100%) | 13/14 (93%) | +7% |
| Air-gapped 2.9 fields | 11/11 (100%) | 6/11 (55%) | +45% |
| Add app in repo | 7/7 (100%) | 7/7 (100%) | 0% |
| Debug broken overlay | 5/5 (100%) | 5/5 (100%) | 0% |
| ResourceSet local render | 6/6 (100%) | 6/6 (100%) | 0% |
| Monorepo delivery | 9/9 (100%) | 3/9 (33%) | +67% |
| Staged Jobs | 9/9 (100%) | 6/9 (67%) | +33% |
| Fleet scaffold | 10/10 (100%) | 1/10 (10%) | +90% |
| **Overall** | **149/149 (100%)** | **113/149 (76%)** | **+24%** |

**Costs**

| Metric | With Skill | Baseline |
|--------|-----------|----------|
| Mean duration | 219s | 222s |
| Mean tokens | 71.2k | 54.6k |

---

Model: `claude-sonnet-5`

**Results**

| Eval | With Skill | Baseline | Delta |
|------|-----------|----------|-------|
| OCI Helm chart | 11/11 (100%) | 8/11 (73%) | +27% |
| ResourceSet preview envs | 15/15 (100%) | 10/15 (67%) | +33% |
| Notifications | 16/16 (100%) | 15/16 (94%) | +6% |
| Image automation | 9/9 (100%) | 6/9 (67%) | +33% |
| FluxInstance + ResourceSets | 15/15 (100%) | 10/15 (67%) | +33% |
| Terraform bootstrap | 12/12 (100%) | 9/12 (75%) | +25% |
| Gitless publish pipeline | 14/14 (100%) | 12/14 (86%) | +14% |
| Air-gapped 2.9 fields | 11/11 (100%) | 10/11 (91%) | +9% |
| Add app in repo | 7/7 (100%) | 7/7 (100%) | 0% |
| Debug broken overlay | 5/5 (100%) | 5/5 (100%) | 0% |
| ResourceSet local render | 6/6 (100%) | 5/6 (83%) | +17% |
| Monorepo delivery | 9/9 (100%) | 5/9 (56%) | +44% |
| Staged Jobs | 9/9 (100%) | 8/9 (89%) | +11% |
| Fleet scaffold | 10/10 (100%) | 2/10 (20%) | +80% |
| **Overall** | **149/149 (100%)** | **112/149 (75%)** | **+25%** |

**Costs**

| Metric | With Skill | Baseline |
|--------|-----------|----------|
| Mean duration | 133s | 188s |
| Mean tokens | 74.5k | 64.8k |

## v0.2.0 (2026-07-03)

The bundled OpenAPI JSON schemas are replaced with greppable field indexes (`assets/schemas/*.fields.txt`, one line per field), shrinking the schema payload by ~45%. Agents grep a dotted field path instead of reading the full JSON. Three-way comparison against the v0.1.0 snapshot (JSON schemas) and the no-skill baseline; 3 runs per eval per configuration, assertion counts pooled across runs. Graded by `claude-opus-4-8`.

Model: `claude-sonnet-5`

**Results**

| Eval | v0.2.0 | v0.1.0 | Baseline | Delta |
|------|--------|--------|----------|-------|
| OCI Helm chart | 33/33 (100%) | 33/33 (100%) | 24/33 (73%) | +27% |
| ResourceSet preview envs | 45/45 (100%) | 45/45 (100%) | 33/45 (73%) | +27% |
| Notifications | 48/48 (100%) | 48/48 (100%) | 45/48 (94%) | +6% |
| Image automation | 27/27 (100%) | 27/27 (100%) | 18/27 (67%) | +33% |
| FluxInstance + ResourceSets | 45/45 (100%) | 45/45 (100%) | 38/45 (84%) | +16% |
| Terraform bootstrap | 36/36 (100%) | 36/36 (100%) | 18/36 (50%) | +50% |
| Gitless publish pipeline | 42/42 (100%) | 42/42 (100%) | 39/42 (93%) | +7% |
| Air-gapped 2.9 fields | 33/33 (100%) | 33/33 (100%) | 15/33 (45%) | +55% |
| Add app in repo | 21/21 (100%) | 21/21 (100%) | 21/21 (100%) | 0% |
| Debug broken overlay | 15/15 (100%) | 15/15 (100%) | 15/15 (100%) | 0% |
| ResourceSet local render | 18/18 (100%) | 18/18 (100%) | 15/18 (83%) | +17% |
| **Overall** | **363/363 (100%)** | **363/363 (100%)** | **281/363 (77%)** | **+23%** |

**Costs**

| Metric | v0.2.0 | v0.1.0 | Baseline |
|--------|--------|--------|----------|
| Mean duration | 193s | 187s | 196s |
| Mean tokens | 62.2k | 63.1k | 41.8k |

## v0.1.0 (2026-07-02)

Suite grows from 86 to 121 assertions with new evals: `airgapped-2.9-fields`, `add-app-in-repo`, `debug-broken-overlay`, and `resourceset-local-render` — the last three run against repo fixtures and exercise the new `flux-cli` reference (repo discovery, local rendering, and `flux schema validate` as an authoring control loop). `preview-envs` rewritten for the least-privilege pattern, `gitless` gained a `flux schema validate` assertion.

Model: `claude-opus-4-8`

**Results**

| Eval | With Skill | Baseline | Delta |
|------|-----------|----------|-------|
| OCI Helm chart | 11/11 (100%) | 8/11 (73%) | +27% |
| ResourceSet preview envs | 15/15 (100%) | 5/15 (33%) | +67% |
| Notifications | 16/16 (100%) | 15/16 (94%) | +6% |
| Image automation | 9/9 (100%) | 6/9 (67%) | +33% |
| FluxInstance + ResourceSets | 15/15 (100%) | 13/15 (87%) | +13% |
| Terraform bootstrap | 12/12 (100%) | 5/12 (42%) | +58% |
| Gitless publish pipeline | 14/14 (100%) | 12/14 (86%) | +14% |
| Air-gapped 2.9 fields | 11/11 (100%) | 6/11 (55%) | +45% |
| Add app in repo | 7/7 (100%) | 7/7 (100%) | 0% |
| Debug broken overlay | 5/5 (100%) | 5/5 (100%) | 0% |
| ResourceSet local render | 6/6 (100%) | 5/6 (83%) | +17% |
| **Overall** | **121/121 (100%)** | **87/121 (72%)** | **+28%** |

**Costs**

| Metric | With Skill | Baseline |
|--------|-----------|----------|
| Mean duration | 122s | 100s |
| Mean tokens | 47.9k | 25.6k |

---

Model: `claude-sonnet-5`

**Results**

| Eval | With Skill | Baseline | Delta |
|------|-----------|----------|-------|
| OCI Helm chart | 11/11 (100%) | 8/11 (73%) | +27% |
| ResourceSet preview envs | 15/15 (100%) | 8/15 (53%) | +47% |
| Notifications | 16/16 (100%) | 15/16 (94%) | +6% |
| Image automation | 9/9 (100%) | 6/9 (67%) | +33% |
| FluxInstance + ResourceSets | 15/15 (100%) | 8/15 (53%) | +47% |
| Terraform bootstrap | 10/12 (83%) | 5/12 (42%) | +41% |
| Gitless publish pipeline | 14/14 (100%) | 12/14 (86%) | +14% |
| Air-gapped 2.9 fields | 11/11 (100%) | 5/11 (45%) | +55% |
| Add app in repo | 7/7 (100%) | 7/7 (100%) | 0% |
| Debug broken overlay | 5/5 (100%) | 5/5 (100%) | 0% |
| ResourceSet local render | 6/6 (100%) | 5/6 (83%) | +17% |
| **Overall** | **119/121 (98%)** | **84/121 (69%)** | **+29%** |

**Costs**

| Metric | With Skill | Baseline |
|--------|-----------|----------|
| Mean duration | 141s | 154s |
| Mean tokens | 59.5k | 37.1k |

## v0.0.4 (2026-06-10)

Model: `claude-fable-5`

**Results**

| Eval | With Skill | Baseline | Delta |
|------|-----------|----------|-------|
| OCI Helm chart | 11/11 (100%) | 9/11 (82%) | +18% |
| ResourceSet preview envs | 12/12 (100%) | 12/12 (100%) | 0% |
| Notifications | 16/16 (100%) | 14/16 (88%) | +12% |
| Image automation | 9/9 (100%) | 9/9 (100%) | 0% |
| FluxInstance + ResourceSets | 14/14 (100%) | 14/14 (100%) | 0% |
| Terraform bootstrap | 12/12 (100%) | 7/12 (58%) | +42% |
| Gitless publish pipeline | 12/12 (100%) | 11/12 (92%) | +8% |
| **Overall** | **86/86 (100%)** | **76/86 (88%)** | **+12%** |

**Costs**

| Metric | With Skill | Baseline |
|--------|-----------|----------|
| Mean duration | 112s | 82s |
| Mean tokens | 36.3k | 18.4k |

## v0.0.2 (2026-03-20)

Model: `claude-opus-4-6`

**Results**

| Eval | With Skill | Baseline | Delta |
|------|-----------|----------|-------|
| OCI Helm chart | 11/11 (100%) | 4/11 (36%) | +64% |
| ResourceSet preview envs | 12/12 (100%) | 9/12 (75%) | +25% |
| Notifications | 16/16 (100%) | 14/16 (88%) | +12% |
| Image automation | 9/9 (100%) | 6/9 (67%) | +33% |
| FluxInstance + ResourceSets | 14/14 (100%) | 9/14 (64%) | +36% |
| **Overall** | **62/62 (100%)** | **42/62 (68%)** | **+32%** |

**Costs**

| Metric | With Skill | Baseline |
|--------|-----------|----------|
| Mean duration | 53s | 28s |
| Mean tokens | 37.9k | 11.7k |

---

Model: `claude-sonnet-4-6`

**Results**

| Eval | With Skill | Baseline | Delta |
|------|-----------|----------|-------|
| OCI Helm chart | 11/11 (100%) | 3/11 (27%) | +73% |
| ResourceSet preview envs | 12/12 (100%) | 7/12 (58%) | +42% |
| Notifications | 16/16 (100%) | 15/16 (94%) | +6% |
| Image automation | 9/9 (100%) | 6/9 (67%) | +33% |
| FluxInstance + ResourceSets | 14/14 (100%) | 11/14 (79%) | +21% |
| **Overall** | **62/62 (100%)** | **42/62 (68%)** | **+32%** |

**Costs**

| Metric | With Skill | Baseline |
|--------|-----------|----------|
| Mean duration | 66s | 45s |
| Mean tokens | 39.2k | 12.3k |

---

Model: `claude-haiku-4-5`

**Results**

| Eval | With Skill | Baseline | Delta |
|------|-----------|----------|-------|
| OCI Helm chart | 10/11 (91%) | 4/11 (36%) | +55% |
| ResourceSet preview envs | 12/12 (100%) | 3/12 (25%) | +75% |
| Notifications | 16/16 (100%) | 11/16 (69%) | +31% |
| Image automation | 9/9 (100%) | 6/9 (67%) | +33% |
| FluxInstance + ResourceSets | 14/14 (100%) | 2/14 (14%) | +86% |
| **Overall** | **61/62 (98%)** | **26/62 (42%)** | **+56%** |

**Costs**

| Metric | With Skill | Baseline |
|--------|-----------|----------|
| Mean duration | 40s | 30s |
| Mean tokens | 30.4k | 17.8k |

## v0.0.1 (2026-03-14)

Model: `claude-opus-4-6`

**Results**

| Eval | With Skill | Baseline | Delta |
|------|-----------|----------|-------|
| OCI Helm chart | 11/11 (100%) | 4/11 (36%) | +64% |
| ResourceSet preview envs | 12/12 (100%) | 9/12 (75%) | +25% |
| Notifications | 16/16 (100%) | 13/16 (81%) | +19% |
| Image automation | 9/9 (100%) | 6/9 (67%) | +33% |
| FluxInstance + ResourceSets | 14/14 (100%) | 10/14 (71%) | +29% |
| **Overall** | **62/62 (100%)** | **42/62 (68%)** | **+32%** |

**Costs**

| Metric | With Skill | Baseline |
|--------|-----------|----------|
| Mean duration | 41s | 20s |
| Mean tokens | 36.9k | 11.2k |

---

Model: `claude-sonnet-4-6`

**Results**

| Eval | With Skill | Baseline | Delta |
|------|-----------|----------|-------|
| OCI Helm chart | 11/11 (100%) | 3/11 (27%) | +73% |
| ResourceSet preview envs | 12/12 (100%) | 6/12 (50%) | +50% |
| Notifications | 16/16 (100%) | 12/16 (75%) | +25% |
| Image automation | 9/9 (100%) | 6/9 (67%) | +33% |
| FluxInstance + ResourceSets | 14/14 (100%) | 11/14 (79%) | +21% |
| **Overall** | **62/62 (100%)** | **38/62 (61%)** | **+39%** |

**Costs**

| Metric | With Skill | Baseline |
|--------|-----------|----------|
| Mean duration | 50s | 29s |
| Mean tokens | 39.2k | 11.3k |
