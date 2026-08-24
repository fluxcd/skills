# gitops-repo-audit

## v0.3.0 (2026-08-25)

Adds ResourceSet pipeline checks (`spec.steps` + `wait`, Job `force`/`recreateOnFailure`/`ttlSecondsAfterFinished`, step vs applier timeouts, `dependsOn` namespaces), post-build substitution rules (YAML-safe values, plain `spec.images[].name`, same-namespace `substituteFrom`), the directory-driven monorepo pattern (ArtifactGenerator `pathPattern` + `ExternalArtifact` input provider), the `source-watcher` prerequisite, and a `multitenant: false` exception for cross-namespace refs. New fixture `tests/gitops-repo-audit/resourceset-pipelines` (eval 7) seeds each defect. Compared against the v0.2.0 skill snapshot (no no-skill baseline); 1 run per eval per configuration. Graded by `claude-opus-5`.

Model: `claude-opus-5`

**Results**

| Eval | v0.3.0 | v0.2.0 | Delta |
|------|--------|--------|-------|
| Monorepo structure | 14/14 (100%) | 14/14 (100%) | 0% |
| Multi-repo fleet | 16/16 (100%) | 16/16 (100%) | 0% |
| Image automation | 14/14 (100%) | 13/14 (93%) | +7% |
| Mixed issues | 21/21 (100%) | 21/21 (100%) | 0% |
| Overlay effects | 5/5 (100%) | 5/5 (100%) | 0% |
| Overlay stress | 6/6 (100%) | 6/6 (100%) | 0% |
| ResourceSet pipelines | 16/16 (100%) | 11/16 (69%) | +31% |
| **Overall** | **92/92 (100%)** | **86/92 (93%)** | **+7%** |

The v0.2.0 skill missed or inverted five ResourceSet-pipeline findings: it never flagged the `dependsOn` entry without `namespace`, praised `app_semver: ">=1.0.0"` as a "bounded, stable-only range" instead of catching the invalid YAML after substitution, recommended *adding* `ttlSecondsAfterFinished` to the second Job rather than removing it, listed `recreateOnFailure` on the DB migration under "done well", and rated the intentional cross-namespace `sourceRef` (`multitenant: false`) as Critical.

**Costs**

| Metric | v0.3.0 | v0.2.0 |
|--------|--------|--------|
| Mean duration | 291s | 267s |
| Mean tokens | 99.2k | 89.0k |

## v0.2.0 (2026-07-03)

The bundled OpenAPI JSON schemas are replaced with greppable field indexes (`assets/schemas/*.fields.txt`, one line per field), shrinking the schema payload by ~45%. Agents grep a dotted field path instead of reading the full JSON. Three-way comparison against the v0.1.0 snapshot (JSON schemas) and the no-skill baseline; 3 runs per eval per configuration, assertion counts pooled across runs. Graded by `claude-opus-4-8`.

Model: `claude-sonnet-5`

**Results**

| Eval | v0.2.0 | v0.1.0 | Baseline | Delta |
|------|--------|--------|----------|-------|
| Monorepo structure | 41/42 (98%) | 42/42 (100%) | 31/42 (74%) | +24% |
| Multi-repo fleet | 48/48 (100%) | 47/48 (98%) | 30/48 (62%) | +38% |
| Image automation | 36/42 (86%) | 35/42 (83%) | 21/42 (50%) | +36% |
| Mixed issues | 60/63 (95%) | 61/63 (97%) | 40/63 (63%) | +32% |
| Overlay effects | 15/15 (100%) | 15/15 (100%) | 15/15 (100%) | 0% |
| Overlay stress | 18/18 (100%) | 18/18 (100%) | 18/18 (100%) | 0% |
| **Overall** | **218/228 (96%)** | **218/228 (96%)** | **155/228 (68%)** | **+28%** |

**Costs**

| Metric | v0.2.0 | v0.1.0 | Baseline |
|--------|--------|--------|----------|
| Mean duration | 379s | 370s | 304s |
| Mean tokens | 85.8k | 85.7k | 67.4k |

## v0.1.0 (2026-07-02)

Suite grows from 65 to 76 assertions with new evals: `overlay-effects`, `overlay-stress`. The `monorepo-structure` fixture gained the `source-watcher` component on its FluxInstances (required by its ArtifactGenerator pipeline).

Model: `claude-opus-4-8`

**Results**

| Eval | With Skill | Baseline | Delta |
|------|-----------|----------|-------|
| Monorepo structure | 14/14 (100%) | 9/14 (64%) | +36% |
| Multi-repo fleet | 15/16 (94%) | 10/16 (62%) | +32% |
| Image automation | 12/14 (86%) | 10/14 (71%) | +15% |
| Mixed issues | 21/21 (100%) | 14/21 (67%) | +33% |
| Overlay effects | 5/5 (100%) | 5/5 (100%) | 0% |
| Overlay stress | 6/6 (100%) | 6/6 (100%) | 0% |
| **Overall** | **73/76 (96%)** | **54/76 (71%)** | **+25%** |

**Costs**

| Metric | With Skill | Baseline |
|--------|-----------|----------|
| Mean duration | 203s | 236s |
| Mean tokens | 58.4k | 42.7k |

---

Model: `claude-sonnet-5`

**Results**

| Eval | With Skill | Baseline | Delta |
|------|-----------|----------|-------|
| Monorepo structure | 14/14 (100%) | 11/14 (79%) | +21% |
| Multi-repo fleet | 13/16 (81%) | 11/16 (69%) | +12% |
| Image automation | 13/14 (93%) | 8/14 (57%) | +36% |
| Mixed issues | 20/21 (95%) | 13/21 (62%) | +33% |
| Overlay effects | 5/5 (100%) | 5/5 (100%) | 0% |
| Overlay stress | 6/6 (100%) | 6/6 (100%) | 0% |
| **Overall** | **71/76 (93%)** | **54/76 (71%)** | **+22%** |

**Costs**

| Metric | With Skill | Baseline |
|--------|-----------|----------|
| Mean duration | 299s | 249s |
| Mean tokens | 69.5k | 54.8k |

## v0.0.4 (2026-06-11)

Model: `claude-fable-5`

**Results**

| Eval | With Skill | Baseline | Delta |
|------|-----------|----------|-------|
| Monorepo structure | 14/14 (100%) | 11/14 (79%) | +21% |
| Multi-repo fleet | 15/16 (94%) | 15/16 (94%) | 0% |
| Image automation | 14/14 (100%) | 12/14 (86%) | +14% |
| Mixed issues | 21/21 (100%) | 15/21 (71%) | +29% |
| **Overall** | **64/65 (98%)** | **53/65 (82%)** | **+16%** |

**Costs**

| Metric | With Skill | Baseline |
|--------|-----------|----------|
| Mean duration | 212s | 209s |
| Mean tokens | 53.5k | 33.4k |

## v0.0.2 (2026-03-20)

Model: `claude-opus-4-6`

**Results**

| Eval | With Skill | Baseline | Delta |
|------|-----------|----------|-------|
| Monorepo structure | 14/14 (100%) | 12/14 (86%) | +14% |
| Multi-repo fleet | 16/16 (100%) | 14/16 (88%) | +12% |
| Image automation | 14/14 (100%) | 13/14 (93%) | +7% |
| Mixed issues | 20/21 (95%) | 14/21 (67%) | +28% |
| **Overall** | **64/65 (98%)** | **53/65 (82%)** | **+16%** |

**Costs**

| Metric | With Skill | Baseline |
|--------|-----------|----------|
| Mean duration | 183s | 111s |
| Mean tokens | 48.9k | 22.8k |

---

Model: `claude-sonnet-4-6`

**Results**

| Eval | With Skill | Baseline | Delta |
|------|-----------|----------|-------|
| Monorepo structure | 14/14 (100%) | 9/14 (64%) | +36% |
| Multi-repo fleet | 15/16 (94%) | 10/16 (63%) | +31% |
| Image automation | 11/14 (79%) | 10/14 (71%) | +8% |
| Mixed issues | 21/21 (100%) | 11/21 (52%) | +48% |
| **Overall** | **61/65 (94%)** | **40/65 (62%)** | **+32%** |

**Costs**

| Metric | With Skill | Baseline |
|--------|-----------|----------|
| Mean duration | 210s | 129s |
| Mean tokens | 42.7k | 23.2k |

---

Model: `claude-haiku-4-5`

**Results**

| Eval | With Skill | Baseline | Delta |
|------|-----------|----------|-------|
| Monorepo structure | 14/14 (100%) | 9/14 (64%) | +36% |
| Multi-repo fleet | 14/16 (88%) | 13/16 (81%) | +7% |
| Image automation | 11/14 (79%) | 8/14 (57%) | +22% |
| Mixed issues | 16/21 (76%) | 9/21 (43%) | +33% |
| **Overall** | **55/65 (85%)** | **39/65 (60%)** | **+25%** |

**Costs**

| Metric | With Skill | Baseline |
|--------|-----------|----------|
| Mean duration | 108s | 90s |
| Mean tokens | 41.2k | 26.6k |

## v0.0.1 (2026-03-14)

Model: `claude-opus-4-6`

**Results**

| Eval | With Skill | Baseline | Delta |
|------|-----------|----------|-------|
| Monorepo structure | 14/14 (100%) | 11/14 (79%) | +21% |
| Multi-repo fleet | 16/16 (100%) | 13/16 (81%) | +19% |
| Image automation | 12/12 (100%) | 10/12 (83%) | +17% |
| Mixed issues | 18/20 (90%) | 14/20 (70%) | +20% |
| **Overall** | **60/62 (97%)** | **48/62 (77%)** | **+20%** |

**Costs**

| Metric | With Skill | Baseline |
|--------|-----------|----------|
| Mean duration | 134s | 91s |
| Mean tokens | 37.4k | 20.5k |

---

Model: `claude-sonnet-4-6`

**Results**

| Eval | With Skill | Baseline | Delta |
|------|-----------|----------|-------|
| Monorepo structure | 14/14 (100%) | 9/14 (64%) | +36% |
| Multi-repo fleet | 16/16 (100%) | 14/16 (88%) | +12% |
| Image automation | 11/12 (92%) | 11/12 (92%) | 0% |
| Mixed issues | 20/20 (100%) | 12/20 (60%) | +40% |
| **Overall** | **61/62 (98%)** | **46/62 (74%)** | **+24%** |

**Costs**

| Metric | With Skill | Baseline |
|--------|-----------|----------|
| Mean duration | 238s | 140s |
| Mean tokens | 45.3k | 24.4k |
