# Implementation specification — agriWaveInfer 1.0.0

## Consolidation rule

Version 1.0.0 does not introduce a new estimator. It consolidates the
complete implementation from 0.1.0–0.9.0, freezes the public API,
reconciles release metadata, strengthens release-level tests, and
packages the validation/publication evidence. Runtime certification
remains evidence-dependent.

# Implementation specification — agriWaveInfer 1.0.0

## Scope

Scientific validation and publication layer only. No new wavelet
estimator is introduced.

## Files

- `R/workflow-spec-s7.R`: `AwiWorkflowSpec`.
- `R/publication.R`: workflow orchestration, report, bundle, publication
  check.
- `paper/`: JOSS-compatible scaffold.
- `inst/validation/publication_layer_smoke.R`: runtime
  source/publication check.
- `vignettes/v16-reproducible-workflows.Rmd` and
  `v17-publication-and-validation.Rmd`.

## Required 1.0.0 evidence

The exact 1.0.0 tarball must pass installation in a clean library,
`testthat`, differential numerical tests, truth-known recovery tests,
vignettes, metadata reconciliation, and `R CMD check --as-cran`.
Source-level validation in 1.0.0 cannot satisfy those runtime gates.

## R compatibility decision

The package minimum is raised to **R \>= 4.3.0** in 1.0.0 because the
implementation consistently uses native S7 `object@property` access. S7
documents that this syntax requires an additional compatibility
namespace workaround on older R versions. The release chooses an
explicit modern minimum rather than claiming unsupported R 4.1/4.2
compatibility. `S7::S7_on_load()` replaces the backward-compatible
legacy name `methods_register()` in `.onLoad()`.
