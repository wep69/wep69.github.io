# Estimate sequential multiscale propagation and descriptive cumulative lag

Evaluates an ordered series of adjacent time-frequency relations, such
as precipitation to soil moisture to vegetation to yield, after explicit
sign harmonization.

## Usage

``` r
awi_propagation(chain, period_range, time_range = NULL,
  spec = awi_propagation_spec(), wavelet_spec = awi_wavelet_spec(),
  backend = wavelet_spec@backend, quiet = TRUE)
awi_propagation_lag(x, require_supported_chain = TRUE)
```

## Arguments

- chain:

  An `awi_drought_chain` in the hypothesized node order.

- period_range:

  Required two-element period band for the propagation question.

- time_range:

  Optional two-element physical-time window.

- spec:

  An `AwiPropagationSpec`.

- wavelet_spec:

  An `AwiWaveletSpec`.

- backend:

  Wavelet backend used by
  [`awi_wtc()`](https://wep69.github.io/agriWaveInfer/reference/awi_wavelet.md).

- quiet:

  Suppress progress messages.

- x:

  An `awi_propagation` result.

- require_supported_chain:

  If `TRUE`, withhold cumulative lag when any adjacent edge fails the
  propagation-support rule.

## Value

`awi_propagation()` returns an `awi_propagation` object with adjacent
WTC fields, circular lag summaries, edge diagnostics, inference tier,
and chain-support status. `awi_propagation_lag()` returns edge and
cumulative descriptive lags.

## Details

Each adjacent edge is analyzed independently in the requested
time-period domain. The cumulative lag is the sum of adjacent
principal-branch band summaries and is therefore descriptive. It is
withheld by default if the expected chain is not supported on every
edge. The output always records `causal_effect = FALSE` at the
cumulative-lag layer.

## References

Xu Z, Jiang B, Guo X, Wu Z, Fan S (2026). Initial soil moisture
conditions dominate variation in event-scale propagation time from
meteorological to agricultural drought. Agricultural Water Management
325:110128. doi:10.1016/j.agwat.2026.110128.

Sun P, Liu R, Yao R, Gu X, Gulakhmadov A, Kong D, Zhang X (2025).
Propagation threshold from meteorological to agricultural drought and
its potential influence factors. Journal of Hydrology 655:132920.
doi:10.1016/j.jhydrol.2025.132920.
