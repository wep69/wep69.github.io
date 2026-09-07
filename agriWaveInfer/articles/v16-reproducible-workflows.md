# Reproducible multiscale workflows

``` r

library(agriWaveInfer)
workflow_policy <- awi_workflow_spec(
  selection = "cea",
  phase_summary = TRUE,
  conditional_summary = FALSE,
  strict = TRUE
)

fit <- awi_workflow(
  y = yield_series,
  drivers = list(ENSO = enso, PDO = pdo, rain = rainfall),
  period_range = c(24, 84),
  workflow_spec = workflow_policy
)

awi_report(fit, "analysis-report.md")
awi_reproducibility_bundle(fit, "analysis-bundle")
```

The workflow layer orchestrates already-defined inferential functions
rather than introducing a new statistical estimator. Every step retains
its underlying specification object and the workflow records failures,
warnings, package/runtime provenance, and the selected driver set.
Directional summaries remain association-based and do not identify
causal effects.
