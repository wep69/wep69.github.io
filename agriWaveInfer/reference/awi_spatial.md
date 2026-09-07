# Spatial wavelet coherence, dominant-driver, and lag maps

Summarizes multiscale wavelet relationships across spatially indexed
response series while controlling memory use and preserving the declared
time-period inference domain.

## Usage

``` r
awi_spatial_coherence(response, driver, coords,
  period_range = NULL, time_range = NULL,
  wavelet_spec = awi_wavelet_spec(),
  phase_spec = awi_phase_spec(require_significance = FALSE),
  spatial_spec = awi_spatial_spec(), significance = FALSE,
  nrands = 300L, sig_level = 0.95, min_coherence = 0.5,
  quiet = TRUE)

awi_coherence_map(x, metric = c("awc", "posc", "persistence"),
  output = c("data.frame", "terra", "stars"))

awi_driver_map(response, drivers, coords, method = c("single", "cea"),
  period_range = NULL, time_range = NULL,
  wavelet_spec = awi_wavelet_spec(),
  condition = awi_condition_spec(method = "svd"),
  null = awi_null_spec(),
  driver_spec = awi_driver_spec(metric = "awc", strategy = "cea"),
  spatial_spec = awi_spatial_spec(),
  output = c("data.frame", "terra", "stars"), quiet = TRUE)

awi_lag_map(response, driver = NULL, coords = NULL,
  period_range = NULL, time_range = NULL,
  wavelet_spec = awi_wavelet_spec(),
  phase_spec = awi_phase_spec(require_significance = FALSE),
  spatial_spec = awi_spatial_spec(), significance = FALSE,
  nrands = 300L, sig_level = 0.95, min_coherence = 0.5,
  output = c("data.frame", "terra", "stars"), quiet = TRUE)
```

## Arguments

- response:

  For `awi_spatial_coherence()` and `awi_driver_map()`, a named list of
  aligned `awi_series`, one per spatial location. For `awi_lag_map()`,
  either that list or a precomputed `awi_spatial_coherence` object.

- driver:

  One common `awi_series` applied to all locations, or a
  location-specific list with one aligned series per response.

- drivers:

  Named list of common candidate drivers evaluated at each response
  location.

- coords:

  Data frame with unique columns `id`, `x`, and `y`; `id` must match
  response names.

- period_range:

  Optional two-element physical-period band.

- time_range:

  Optional two-element physical-time window.

- wavelet_spec:

  An `AwiWaveletSpec`.

- phase_spec:

  An `AwiPhaseSpec` controlling lag and phase summaries.

- spatial_spec:

  An `AwiSpatialSpec` controlling chunking, memory, errors, parallel
  workers, field retention, and CRS.

- significance:

  Whether backend significance is requested for each location.

- nrands:

  Number of backend randomizations when significance is requested.

- sig_level:

  Backend significance level.

- min_coherence:

  Cell-level minimum coherence used in persistence and lag eligibility.

- quiet:

  Suppress progress messages from underlying wavelet calls.

- x:

  An `awi_spatial_coherence` result.

- metric:

  Coherence-map value: average wavelet coherence, Percentage of
  Significant Coherence, or coherence persistence.

- output:

  Representation of the mapped value. `"data.frame"` always works.
  `"terra"` requires optional terra; `"stars"` currently requires both
  terra and stars. Raster outputs require a regular x-y grid.

- method:

  Dominant-driver strategy. `"single"` maps the top individually
  screened driver; `"cea"` maps the first driver in the retained
  coherence-elimination set and stores the complete selected set.

- condition:

  An `AwiConditionSpec` used by multivariate driver calculations.

- null:

  An `AwiNullSpec`, used when the selected driver metric or conditional
  check requires surrogate inference.

- driver_spec:

  An `AwiDriverSpec` controlling the selection metric, stability
  criteria, and CEA settings.

## Value

`awi_spatial_coherence()` returns an `awi_spatial_coherence` with one
summary row per location, an explicit memory plan, and optional retained
fields. `awi_coherence_map()` returns an `awi_coherence_map`.
`awi_driver_map()` returns an `awi_driver_map` plus a categorical
driver-code lookup. `awi_lag_map()` returns an `awi_lag_map`.

## Details

Spatial summaries are computed by location in memory-aware chunks. The
default discards full wavelet fields after requested summaries are
extracted. Driver dominance means strongest retained multiscale
association under the declared rule, not causal attribution.
Phase-derived lag is a principal circular lag and does not identify a
causal effect. Optional raster conversion is a representation layer; it
does not alter the underlying inference.

## References

Sankaran S, et al. (2026). Wavelet-based assessment of climate
oscillation and meteorological influences on cereal yield variability
across India's agro-climatic zones. Theoretical and Applied Climatology.
doi:10.1007/s00704-026-06085-2.
