# Function contracts — agriWaveInfer 1.0.0

This document freezes the 1.0.0 public surface. Function signatures and
scientific semantics are inherited from the fully specified 0.9.0 tree
unless explicitly corrected in release metadata. No new public function
is introduced in 1.0.0.

See `API_FREEZE_1.0.0.md` for the canonical export set and
`API_LIFECYCLE.md` for compatibility policy.

# Function contracts — agriWaveInfer 1.0.0

## New public contracts

### `awi_workflow_spec()` / `AwiWorkflowSpec`

Defines orchestration, strictness, significance, intermediate retention,
and provenance policies. Invalid enum, probability, integer, or logical
values must fail validation.

### `awi_workflow()`

Inputs: one response `awi_series`, named drivers, optional controls, and
existing scientific specification objects. Output: `awi_workflow` with
deterministic step table, selected/primary driver, optional intermediate
results, provenance, and `causal_effect = FALSE`. It must not silently
redefine an underlying statistical method.

### `awi_report()`

Produces Markdown or text summaries. Reports must carry a
causal-interpretation safeguard and must not convert source-level
evidence into runtime-certification claims.

### `awi_reproducibility_bundle()`

Writes `analysis.rds`, `report.md`, provenance, `sessionInfo.txt`,
workflow summaries when available, and a payload-file manifest. SHA-256
is used when optional package `digest` is available; otherwise MD5 is
explicitly labeled.

### `awi_publication_check()`

Checks required package/publication files, version reconciliation across
DESCRIPTION/CITATION.cff/CodeMeta, and provisional markers. It is a
source-tree check, not a substitute for `R CMD check`.
