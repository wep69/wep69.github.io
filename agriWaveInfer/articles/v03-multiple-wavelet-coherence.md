# Multiple Wavelet Coherence and Local Numerical Diagnostics

## Purpose

The matrix formulation follows the localized multivariate coherence
framework of Hu and Si (Hu and Si 2016). Recent cereal-yield research
also illustrates MWC-based screening of climate and meteorological
drivers (Sankaran et al. 2026).

Multiple wavelet coherence (MWC) summarizes the localized association
between one response and a set of predictors. In agroclimatic work this
permits a question such as: at which times and scales is crop yield
jointly associated with ENSO, rainfall, and temperature?

## Native matrix engine

The default `method = "matrix"` constructs a smoothed complex spectral
matrix and evaluates a multiple-coherence quadratic form. The
implementation accepts an arbitrary number of predictor series subject
to alignment and numerical constraints.

``` r

library(agriWaveInfer)
t <- 1:160
enso <- awi_series(t, sin(2*pi*t/24), variable = "ENSO")
rain <- awi_series(t, 0.6*enso$value + cos(2*pi*t/12), variable = "rain")
temp <- awi_series(t, cos(2*pi*t/40), variable = "temperature")
yld <- awi_series(t, 0.5*enso$value + 0.8*rain$value - 0.3*temp$value,
                  variable = "yield")

m <- awi_mwc(
  yld, list(enso, rain, temp),
  condition = awi_condition_spec(method = "svd")
)
range(m$coherence, na.rm = TRUE)
#> [1] 1 1
```

## Reduction and invariance checks

The numerical test suite requires the one-predictor matrix MWC to reduce
to the corresponding bivariate wavelet coherence within a declared
tolerance. Reordering predictors must not alter the MWC surface apart
from floating-point error.

## Differential oracle

When `vectorwavelet` is installed, `method = "vectorwavelet"` exposes
its n-dimensional vector wavelet coherence implementation as an optional
oracle.
[`awi_mwc_compare()`](https://wep69.github.io/agriWaveInfer/reference/awi_mwc_compare.md)
records differences instead of assuming exact equality, because
smoothing, normalization, scale handling, and regularization decisions
must first be reconciled.

``` r

cmp <- awi_mwc_compare(yld, list(enso, rain, temp))
cmp
#> <awi_mwc_comparison>
#>  comparable: TRUE 
#>  cells compared: 7989 
#>  mean absolute difference: 0.01821744 
#>  max absolute difference: 0.2781428 
#>  within tolerance: FALSE
```

## Collinearity is a scientific diagnostic

Teleconnection indices and weather drivers can be strongly redundant. A
high MWC obtained from a nearly singular local predictor matrix can be
numerically fragile. Therefore condition number, rank, instability, and
regularization are part of the result contract rather than hidden
implementation details.

## Field-aware inference in 0.3.0

For native matrix MWC, version 0.3.0 can surrogate the response while
retaining the observed joint structure among predictors. This is
preferable to independently randomizing correlated teleconnection or
meteorological drivers when the null hypothesis concerns their joint
association with the response.

``` r

null <- awi_null_spec(
  method = "phase",
  n_surrogates = 499,
  seed = 20260904,
  field_method = "cluster_mass"
)

m_sig <- awi_mwc(
  yld, list(enso, rain, temp),
  condition = awi_condition_spec(method = "svd"),
  significance = TRUE,
  null = null
)
```

## What MWC does not answer

A high MWC surface does not identify which predictor is independently
important. That problem belongs to partial coherence and the
driver-attribution layer planned for later versions. MWC also does not
establish causal effects.

## References

Hu, Wei, and Bing Cheng Si. 2016. “Technical Note: Multiple Wavelet
Coherence for Untangling Scale-Specific and Localized Multivariate
Relationships in Geosciences.” *Hydrology and Earth System Sciences* 20:
3183–91. <https://doi.org/10.5194/hess-20-3183-2016>.

Sankaran, Adarsh, Fathima Fazal, Aleena Johnson, Venkataramana Sridhar,
Divyasree Prasad, and Aakhila Fathima Abdul Rasheed. 2026.
“Wavelet-Based Assessment of Climate Oscillation and Meteorological
Influences on Cereal Yield Variability Across India’s Agro-Climatic
Zones.” *Theoretical and Applied Climatology* 157: 147.
<https://doi.org/10.1007/s00704-026-06085-2>.
