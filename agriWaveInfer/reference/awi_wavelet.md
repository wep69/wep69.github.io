# Unified continuous, cross, and coherence wavelet interfaces

Runs unified wavelet analyses through an optional backend.

## Usage

``` r
awi_cwt(x, spec = awi_wavelet_spec(), backend = spec@backend,
  significance = FALSE, lag1 = NULL, sig_level = 0.95)
awi_xwt(x, y, spec = awi_wavelet_spec(), backend = spec@backend,
  lag1 = NULL, sig_level = 0.95)
awi_wtc(x, y, spec = awi_wavelet_spec(), backend = spec@backend,
  significance = FALSE, nrands = 300L, lag1 = NULL, sig_level = 0.95,
  quiet = TRUE)
```

## Arguments

- x,y:

  Aligned `awi_series` objects.

- spec:

  An `AwiWaveletSpec`.

- backend:

  Wavelet backend.

- significance:

  Whether to request backend significance calculations.

- nrands:

  Number of Monte Carlo randomizations when significance is requested.

- lag1:

  Optional AR(1) parameter(s) passed to the backend.

- sig_level:

  Significance level.

- quiet:

  Suppress backend progress output.

## Value

An S3 normalized wavelet-result object retaining the raw backend object
and provenance.
