# Run an auditable multiscale analysis workflow

Orchestrates existing agriWaveInfer layers without changing their
inferential definitions. Driver selection and temporal direction remain
association-based and do not identify causal effects.

## Usage

``` r
awi_workflow(y, drivers, controls = NULL, period_range = NULL,
  time_range = NULL, wavelet_spec = awi_wavelet_spec(),
  condition = awi_condition_spec(method = "svd"), null = awi_null_spec(),
  driver_spec = awi_driver_spec(metric = "awc"),
  phase_spec = awi_phase_spec(require_significance = FALSE),
  workflow_spec = awi_workflow_spec(), quiet = TRUE)
```

## Arguments

- y:

  Response `awi_series`.

- drivers:

  Named list of driver `awi_series` objects.

- controls:

  Optional conditioning series.

- period_range:

  Optional two-element period band.

- time_range:

  Optional two-element time interval.

- wavelet_spec:

  An `AwiWaveletSpec`.

- condition:

  An `AwiConditionSpec`.

- null:

  An `AwiNullSpec`.

- driver_spec:

  An `AwiDriverSpec`.

- phase_spec:

  An `AwiPhaseSpec`.

- workflow_spec:

  An `AwiWorkflowSpec`.

- quiet:

  Suppress progress messages.

## Value

An S3 `awi_workflow` object containing step status, selected drivers,
optional intermediates, and provenance.
