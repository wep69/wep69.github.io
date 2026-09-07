# Multiple wavelet coherence with optional field-aware inference

Quantifies the scale- and time-dependent joint association between a
response and one or more predictors, with explicit numerical and
inferential provenance.

## Usage

``` r
awi_mwc(y, predictors, spec = awi_wavelet_spec(),
  condition = awi_condition_spec(),
  method = c("matrix", "vectorwavelet"),
  significance = FALSE, nrands = 199L, sig_level = 0.95, quiet = TRUE,
  null = awi_null_spec(n_surrogates = nrands, alpha = 1 - sig_level))
```

## Arguments

- y:

  Response `awi_series`. For native surrogate inference this is
  resampled while the predictor set remains fixed, preserving the
  observed dependence among predictors.

- predictors:

  One `awi_series` or a list of predictor series.

- spec:

  An `AwiWaveletSpec`.

- condition:

  An `AwiConditionSpec`.

- method:

  `"matrix"` computes MWC from the smoothed local spectral matrix.
  `"vectorwavelet"` invokes the optional vectorwavelet implementation
  for compatibility or differential validation.

- significance:

  Logical. The native matrix method uses `AwiNullSpec` surrogate
  inference; the optional oracle retains its backend Monte Carlo
  procedure.

- nrands:

  Backend count for `vectorwavelet` and default number of native
  surrogates when `null` is not supplied explicitly.

- sig_level:

  Compatibility significance level. For the default native null
  specification, `alpha = 1 - sig_level`.

- quiet:

  Suppress progress and backend output.

- null:

  An `AwiNullSpec` controlling the native matrix surrogate null and
  field correction.

## Value

An S3 object of class `awi_mwc` and `awi_wavelet_result`, with squared
multiple coherence, local conditioning diagnostics, and optional
empirical field inference.

## Details

The native matrix method uses response surrogates so that the observed
joint dependence structure among predictors is not destroyed merely to
construct the null. Inferential cells inside the cone of influence are
excluded. A significant MWC field does not identify which predictor is
independently important and does not establish causality.

## References

Hu W, Si BC (2016). Technical note: Multiple wavelet coherence for
untangling scale-specific and localized multivariate relationships in
geosciences. Hydrology and Earth System Sciences 20, 3183-3191.
doi:10.5194/hess-20-3183-2016.
