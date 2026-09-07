# Experimental band-limited Granger-style predictive precedence diagnostic

Applies deterministic zero-phase Fourier band filtering followed by
nested autoregressive predictive-precedence comparisons.

## Usage

``` r
awi_band_granger(
  first, second, period_range, time_range = NULL, lag_order = 1L,
  detrend = c("linear", "mean", "none"), alpha = 0.05
)
```

## Arguments

- first,second:

  Aligned regularly sampled `awi_series` objects.

- period_range:

  Two positive period bounds in the same units as the time coordinate.

- time_range:

  Optional two-endpoint window. Filtering and predictive comparison are
  then restricted to this window.

- lag_order:

  Positive integer autoregressive lag order.

- detrend:

  Pre-filter detrending policy.

- alpha:

  Nominal classification threshold.

## Value

An `awi_band_granger` object with nested-model F diagnostics in both
directions.

## Details

This function is experimental. Filtering uses observations from the full
series and can modify dependence structure. The result therefore
describes band-limited predictive precedence under a specific
preprocessing and lag model; it must not be reported as an identified
causal effect.

## References

Granger CWJ (1969). Investigating causal relations by econometric models
and cross-spectral methods. Econometrica 37(3):424-438.
doi:10.2307/1912791.
