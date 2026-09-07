# Summarize coherence over an explicit time-frequency domain

Computes Average Wavelet Coherence (AWC) or Percentage of Significant
Coherence (PoSC) over a scientifically explicit domain rather than over
an implicit plot extent.

## Usage

``` r
awi_awc(x, period_range = NULL, time_range = NULL,
  significant_only = FALSE, mask_coi = TRUE,
  weighting = c("area", "cell"))

awi_posc(x, period_range = NULL, time_range = NULL,
  significant = NULL, mask_coi = TRUE,
  weighting = c("area", "cell"))
```

## Arguments

- x:

  An `awi_wavelet_result` containing a squared-coherence matrix.

- period_range:

  Optional two-element period interval defining the scale domain.

- time_range:

  Optional two-element time interval defining the temporal domain.

- significant_only:

  For AWC, restrict the average to significant cells. The default
  averages all valid coherence values in the domain.

- significant:

  Optional logical significance matrix or numeric coherence threshold
  field. If omitted, significance is recovered from `x`.

- mask_coi:

  Exclude cells outside the cone-of-influence validity region.

- weighting:

  `"area"` uses discrete time-period cell areas; `"cell"` gives equal
  weight to each lattice cell.

## Value

An S3 object of class `awi_driver_metric` containing the estimate,
numerator/denominator, domain coverage, weighting and provenance. PoSC
is reported as a percentage.

## Details

For area weighting, AWC approximates \\A^{-1}\int\int\_\Omega
R^2(t,s)\\dt\\ds\\; PoSC is 100 times the weighted significant area
divided by the weighted valid area. The default excludes the cone of
influence. Equal-cell weighting is available for compatibility with
analyses that summarize the discrete lattice without quadrature weights.

## References

Zhao R, Biswas A, Zhou Y, Zhou Y, Shi Z, Li H (2018). Identifying
localized and scale-specific multivariate controls of soil organic
matter variations using multiple wavelet coherence. Science of the Total
Environment 643, 548-558. doi:10.1016/j.scitotenv.2018.06.210.

Sankaran A et al. (2026). Wavelet-based assessment of climate
oscillation and meteorological influences on cereal yield variability
across India's agro-climatic zones. Theoretical and Applied Climatology
157, 147. doi:10.1007/s00704-026-06085-2.
