# agriWaveInfer 1.0.0

**Consolidated Scientific Release — source-frozen; runtime certification
pending.**

`agriWaveInfer` provides a reproducible inference layer for multiscale
agroclimatic and environmental systems. Version 1.0.0 freezes the
scientific API developed from 0.1.0 through 0.9.0; it does not introduce
a new methodological domain.

## Installation

``` r

# complete, rebuilding the vignettes (recommended for local validation)
remotes::install_github(
  "wep69/agriWaveInfer",
  dependencies = TRUE,
  build_vignettes = TRUE,
  upgrade = "ask",
  force = FALSE
)

# fast path without vignettes (pak)
# pak::pak("wep69/agriWaveInfer")
```

Site: <https://wep69.github.io/agriWaveInfer>

## Publication-oriented workflow

``` r

policy <- awi_workflow_spec(
  selection = "cea",
  phase_summary = TRUE,
  strict = TRUE
)

fit <- awi_workflow(
  y = yield_series,
  drivers = list(ENSO = enso, PDO = pdo, Rain = rainfall),
  period_range = c(24, 84),
  workflow_spec = policy
)

awi_report(fit, "analysis-report.md")
awi_reproducibility_bundle(fit, "analysis-bundle")
awi_publication_check(".", expected_version = "1.0.0")
```

The workflow layer records the underlying specification objects and
execution status. It does not redefine the numerical estimators and does
not turn selected drivers or phase lead into causal effects.

## Spatial workflow

``` r

sp <- awi_spatial_spec(
  chunk_size = 32,
  max_memory_mb = 1024,
  retain_fields = FALSE,
  crs = "EPSG:4326"
)

z <- awi_spatial_coherence(
  response = pixel_series,
  driver = enso,
  coords = coordinates,
  period_range = c(24, 84),
  spatial_spec = sp
)

awc_map <- awi_coherence_map(z, metric = "awc")
lag_map <- awi_lag_map(z)
```

For several candidate drivers:

``` r

dm <- awi_driver_map(
  response = pixel_series,
  drivers = list(ENSO = enso, PDO = pdo, AMO = amo),
  coords = coordinates,
  method = "single",
  driver_spec = awi_driver_spec(metric = "awc")
)
```

## Key safeguards

- The requested period/time domain and COI policy are part of every
  summary.
- Full coefficient fields are discarded by default after spatial
  summaries are calculated.
- Driver dominance is association-based and does not establish
  causality.
- Phase-derived lag uses circular statistics and does not establish a
  causal effect.
- `terra` and `stars` are optional representation backends; they are not
  required by the analytical core.

## Release status

`SOURCE-FROZEN / RUNTIME-PENDING` after source audit and archive
verification. Runtime R gates are documented in `WINDOWS_VALIDATION.md`
and must be executed before claiming full validation.
