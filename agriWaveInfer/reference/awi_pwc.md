# Improved partial wavelet coherence with one or more controls

Computes scale- and time-specific association between `y` and `x` after
conditioning on one or more series, with optional surrogate and
field-aware inference.

## Usage

``` r
awi_pwc(y, x, controls, spec = awi_wavelet_spec(),
  condition = awi_condition_spec(),
  method = c("improved", "legacy_biwavelet"),
  significance = FALSE, nrands = 199L, sig_level = 0.95, quiet = TRUE,
  null = awi_null_spec(n_surrogates = nrands, alpha = 1 - sig_level))

awi_partial(y, x, controls, ...)
```

## Arguments

- y:

  Response `awi_series`. For surrogate inference, this is the series
  resampled under the null while the focal predictor and controls remain
  fixed.

- x:

  Predictor `awi_series`.

- controls:

  One `awi_series` or a non-empty list of conditioning series.

- spec:

  An `AwiWaveletSpec`.

- condition:

  An `AwiConditionSpec`.

- method:

  `"improved"` uses complex smoothed spectral matrices and supports
  multiple controls. `"legacy_biwavelet"` reproduces the compatibility
  implementation in biwavelet and accepts exactly one control.

- significance:

  Logical. For the improved method, generates response surrogates
  according to `null`, calculates empirical p-values, and applies the
  specified field correction. For the legacy method it retains the
  backend Monte Carlo procedure.

- nrands:

  Compatibility count used by the legacy backend and as the default
  number of surrogates when `null` is not supplied explicitly.

- sig_level:

  Compatibility significance level. For the default improved null
  specification, `alpha = 1 - sig_level`.

- quiet:

  Suppress progress messages and backend output.

- null:

  An `AwiNullSpec` controlling surrogate generation and field-aware
  inference for `method = "improved"`.

- ...:

  Arguments passed from `awi_partial()` to `awi_pwc()`.

## Value

An S3 object of class `awi_pwc` and `awi_wavelet_result`. It contains
squared partial coherence, partial phase, COI metadata, local numerical
diagnostics, and, when requested, raw empirical p-values, adjusted
p-values or cluster results, and a significance mask.

## Details

The improved method is based on the complex partial-coherence
formulation of Hu and Si (2021). Under surrogate inference, only the
response is resampled. This retains the observed joint structure among
the focal predictor and conditioning variables while breaking the
response association according to the selected null. Cells inside the
cone of influence are excluded from the inferential domain even when
retained for descriptive display. A phase lead is not a causal-effect
estimate.

## References

Hu W, Si B (2021). Technical Note: Improved partial wavelet coherency
for understanding scale-specific and localized bivariate relationships
in geosciences. Hydrology and Earth System Sciences 25, 321-331.
doi:10.5194/hess-25-321-2021.
