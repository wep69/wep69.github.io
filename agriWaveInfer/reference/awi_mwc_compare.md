# Differential comparison of multiple wavelet coherence implementations

Runs the native spectral-matrix MWC and the optional vectorwavelet
oracle on the same inputs. This function is a validation utility, not a
model-selection criterion.

## Usage

``` r
awi_mwc_compare(y, predictors, spec = awi_wavelet_spec(),
  condition = awi_condition_spec(method = "svd"), tolerance = 1e-08)
```

## Arguments

- y:

  Response `awi_series`.

- predictors:

  At least two predictor `awi_series` objects.

- spec:

  An `AwiWaveletSpec`.

- condition:

  Numerical-conditioning specification for the native calculation.

- tolerance:

  Absolute comparison tolerance.

## Value

An `awi_mwc_comparison` object containing cellwise discrepancy summaries
and both fitted objects.
