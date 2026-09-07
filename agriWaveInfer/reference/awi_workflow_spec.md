# Specify publication-oriented workflow behavior

Defines auditable orchestration choices for the 1.0.0 consolidated
workflow API.

## Usage

``` r
awi_workflow_spec(selection = c("screen", "none", "cea", "exhaustive"),
  phase_summary = TRUE, conditional_summary = FALSE,
  wavelet_significance = FALSE, nrands = 300L, sig_level = 0.95,
  strict = TRUE, store_intermediate = TRUE, capture_provenance = TRUE)
```

## Arguments

- selection:

  Driver-selection layer to execute.

- phase_summary:

  Whether to summarize phase-based direction for the primary driver.

- conditional_summary:

  Whether to compute PWC against supplied controls.

- wavelet_significance:

  Whether the primary WTC requests backend Monte Carlo significance.

- nrands:

  Number of WTC randomizations when requested.

- sig_level:

  WTC significance level.

- strict:

  If `TRUE`, stop at the first failed workflow step; otherwise record
  failures.

- store_intermediate:

  Whether large intermediate analysis objects are retained.

- capture_provenance:

  Whether R/session/backend provenance is captured.

## Value

An S7 `AwiWorkflowSpec` object.
