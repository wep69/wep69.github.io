# agriWaveInfer 0.2.0: Foundations to Conditional and Multivariate Wavelet Inference

## Why an inference-first package?

Wavelet analysis is useful when relationships are localized
simultaneously in time and scale. The foundational literature defines
continuous wavelet analysis and the cross-wavelet/coherence framework
(Torrence and Compo 1998; Grinsted et al. 2004). Bias correction is also
important when comparing power across scales (Liu et al. 2007; Veleda et
al. 2012).

Version 0.3.0 retains that unified foundation and adds conditional and
multivariate inference. The package does not claim novelty for CWT, WTC,
PWC, or MWC in isolation. Its design target is an auditable workflow in
which data contracts, conditioning variables, numerical stability, COI
handling, backend provenance, and interpretation are explicit.

## Scientific data structures

``` r

library(agriWaveInfer)

t <- 1:128
rain <- awi_series(t, sin(2*pi*t/16),
                   variable = "rainfall anomaly", unit = "SD")
ndvi <- awi_series(t, sin(2*pi*t/16 + pi/5),
                   variable = "NDVI anomaly", unit = "SD")
rain
#> <awi_series>
#>  variable: rainfall anomaly 
#>  observations: 128 
#>  time class: integer 
#>  sampling interval: 1 index_units
awi_validate(rain)
#> $valid
#> [1] TRUE
#> 
#> $problems
#> character(0)
#> 
#> $sampling_interval
#> [1] 1
#> 
#> attr(,"class")
#> [1] "awi_validation"
```

## Formal scientific design

``` r

design <- awi_design(
  response = "ndvi",
  predictors = "rainfall",
  time = "month",
  sampling_interval = 1,
  units = list(ndvi = "SD", rainfall = "SD")
)
design
#> <agriWaveInfer::AwiDesign>
#>  @ response         : chr "ndvi"
#>  @ predictors       : chr "rainfall"
#>  @ controls         : chr(0) 
#>  @ time             : chr "month"
#>  @ group            : chr(0) 
#>  @ space            : chr(0) 
#>  @ crop             : chr(0) 
#>  @ units            :List of 2
#>  .. $ ndvi    : chr "SD"
#>  .. $ rainfall: chr "SD"
#>  @ sampling_interval: num 1
#>  @ missing_policy   : chr "error"
#>  @ detrending_policy: chr "none"
```

`AwiDesign` is S7 because invalid combinations of scientific metadata
should be rejected close to construction rather than discovered after a
long workflow.

## Wavelet specification

``` r

spec <- awi_wavelet_spec(
  mother = "morlet",
  omega0 = 6,
  dj = 1/12,
  backend = "auto"
)
spec
#> <agriWaveInfer::AwiWaveletSpec>
#>  @ mother       : chr "morlet"
#>  @ omega0       : num 6
#>  @ dj           : num 0.0833
#>  @ s0           : num NA
#>  @ max_scale    : num NA
#>  @ padding      : logi TRUE
#>  @ normalization: chr "backend"
#>  @ coi_policy   : chr "retain"
#>  @ backend      : chr "auto"
```

Version 0.3.0 deliberately narrows the validated wavelet family to
Morlet. Support for other wavelets should be added only after the
corresponding scale-period and significance behavior is covered by
tests.

## Preflight diagnosis

``` r

awi_capabilities()
#>          backend                             operation implemented installed
#> 1  agriWaveInfer                          surrogate_ar        TRUE      TRUE
#> 2  agriWaveInfer                       surrogate_phase        TRUE      TRUE
#> 3  agriWaveInfer                       surrogate_iaaft        TRUE      TRUE
#> 4  agriWaveInfer                surrogate_moving_block        TRUE      TRUE
#> 5  agriWaveInfer            surrogate_stationary_block        TRUE      TRUE
#> 6  agriWaveInfer           surrogate_block_permutation        TRUE      TRUE
#> 7  agriWaveInfer              surrogate_circular_shift        TRUE      TRUE
#> 8  agriWaveInfer                             field_fdr        TRUE      TRUE
#> 9  agriWaveInfer                         field_cluster        TRUE      TRUE
#> 10 agriWaveInfer                            driver_awc        TRUE      TRUE
#> 11 agriWaveInfer                           driver_posc        TRUE      TRUE
#> 12 agriWaveInfer                      driver_gain_loss        TRUE      TRUE
#> 13 agriWaveInfer                         driver_screen        TRUE      TRUE
#> 14 agriWaveInfer                            driver_cea        TRUE      TRUE
#> 15 agriWaveInfer           driver_exhaustive_selection        TRUE      TRUE
#> 16 agriWaveInfer                            phase_mask        TRUE      TRUE
#> 17 agriWaveInfer                             phase_lag        TRUE      TRUE
#> 18 agriWaveInfer                              lag_band        TRUE      TRUE
#> 19 agriWaveInfer                              lead_map        TRUE      TRUE
#> 20 agriWaveInfer                  phase_directionality        TRUE      TRUE
#> 21 agriWaveInfer                          band_granger        TRUE      TRUE
#> 22 agriWaveInfer                  predictive_direction        TRUE      TRUE
#> 23 agriWaveInfer                            crop_stage        TRUE      TRUE
#> 24 agriWaveInfer                           stage_align        TRUE      TRUE
#> 25 agriWaveInfer                       stage_coherence        TRUE      TRUE
#> 26 agriWaveInfer                         drought_chain        TRUE      TRUE
#> 27 agriWaveInfer                           propagation        TRUE      TRUE
#> 28 agriWaveInfer                       propagation_lag        TRUE      TRUE
#> 29 agriWaveInfer                        network_static        TRUE      TRUE
#> 30 agriWaveInfer                       network_dynamic        TRUE      TRUE
#> 31 agriWaveInfer                         network_scale        TRUE      TRUE
#> 32 agriWaveInfer              network_edge_persistence        TRUE      TRUE
#> 33 agriWaveInfer                       network_compare        TRUE      TRUE
#> 34 agriWaveInfer                     spatial_coherence        TRUE      TRUE
#> 35 agriWaveInfer                 spatial_coherence_map        TRUE      TRUE
#> 36 agriWaveInfer                    spatial_driver_map        TRUE      TRUE
#> 37 agriWaveInfer                       spatial_lag_map        TRUE      TRUE
#> 38 agriWaveInfer              spatial_chunk_processing        TRUE      TRUE
#> 39 agriWaveInfer                workflow_orchestration        TRUE      TRUE
#> 40 agriWaveInfer                       analysis_report        TRUE      TRUE
#> 41 agriWaveInfer                reproducibility_bundle        TRUE      TRUE
#> 42 agriWaveInfer                     publication_check        TRUE      TRUE
#> 43     biwavelet                                   cwt        TRUE      TRUE
#> 44     biwavelet                                   xwt        TRUE      TRUE
#> 45     biwavelet                                   wtc        TRUE      TRUE
#> 46     biwavelet                   pwc_improved_engine        TRUE      TRUE
#> 47     biwavelet              pwc_legacy_compatibility        TRUE      TRUE
#> 48 vectorwavelet               mwc_differential_oracle        TRUE      TRUE
#> 49   WaveletComp                   differential_oracle       FALSE      TRUE
#> 50        igraph              network_interoperability        TRUE      TRUE
#> 51         terra               raster_interoperability        TRUE      TRUE
#> 52         stars spatiotemporal_array_interoperability        TRUE      TRUE
#>    version               validation_tier
#> 1    1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 2    1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 3    1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 4    1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 5    1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 6    1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 7    1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 8    1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 9    1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 10   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 11   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 12   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 13   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 14   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 15   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 16   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 17   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 18   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 19   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 20   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 21   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 22   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 23   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 24   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 25   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 26   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 27   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 28   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 29   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 30   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 31   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 32   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 33   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 34   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 35   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 36   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 37   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 38   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 39   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 40   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 41   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 42   1.0.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 43 0.20.22 SOURCE_FROZEN_RUNTIME_PENDING
#> 44 0.20.22 SOURCE_FROZEN_RUNTIME_PENDING
#> 45 0.20.22 SOURCE_FROZEN_RUNTIME_PENDING
#> 46 0.20.22 SOURCE_FROZEN_RUNTIME_PENDING
#> 47 0.20.22 SOURCE_FROZEN_RUNTIME_PENDING
#> 48   0.1.0 SOURCE_FROZEN_RUNTIME_PENDING
#> 49     1.2                       PLANNED
#> 50   2.3.3 SOURCE_FROZEN_RUNTIME_PENDING
#> 51  1.9.46 SOURCE_FROZEN_RUNTIME_PENDING
#> 52   0.7.3 SOURCE_FROZEN_RUNTIME_PENDING
awi_doctor(rain, operation = "wtc", y = ndvi, spec = spec)
#> <awi_doctor: wtc >
#>  [OK] x_class: x is an awi_series
#>  [OK] x_length: x requires at least 32 observations
#>  [OK] x_regular: x must have regular time spacing for the current wavelet engine
#>  [OK] y_class: y is an awi_series
#>  [OK] y_length: y requires at least 32 observations
#>  [OK] y_regular: y must have regular time spacing for the current wavelet engine
#>  [OK] xy_alignment: x and y must have identical time coordinates; use awi_align() first
#>  [OK] backend: backend available: biwavelet
```

## CWT, XWT and WTC

The following examples require the optional `biwavelet` package.

``` r

cwt <- awi_cwt(rain, spec = spec, significance = FALSE)
xwt <- awi_xwt(rain, ndvi, spec = spec)
wtc <- awi_wtc(rain, ndvi, spec = spec, significance = FALSE)

cwt
#> <awi_cwt>
#>  backend: biwavelet 0.20.22 
#>  observations: 128 
#>  scales: 54
xwt
#> <awi_xwt>
#>  backend: biwavelet 0.20.22 
#>  observations: 128 
#>  scales: 54
wtc
#> <awi_wtc>
#>  backend: biwavelet 0.20.22 
#>  observations: 128 
#>  scales: 54 
#>  coherence range: 0.5950016 to 1.0000000
```

The standardized WTC object exposes a coherence matrix $`R^2(t,s)`$
together with time, scale, period, phase, COI, backend provenance, and
the raw backend object. Coherence is association in the time-frequency
plane; it does not by itself identify a causal effect.

## Conditional and multivariate inference

The main methodological extension in 0.2.0 is the separation of three
related questions. Bivariate WTC describes localized association.
Improved PWC asks whether a focal association remains after conditioning
on one or more specified series (Hu and Si 2021). Matrix MWC measures
the localized joint association between one response and a predictor set
(Hu and Si 2016).

``` r

control <- awi_series(t, cos(2*pi*t/24), variable = "temperature anomaly")
pwc <- awi_pwc(ndvi, rain, controls = control,
               condition = awi_condition_spec(method = "svd"))
mwc <- awi_mwc(ndvi, predictors = list(rain, control),
               condition = awi_condition_spec(method = "svd"))
pwc
#> <awi_pwc>
#>  backend: biwavelet_transform_and_smoothing 0.20.22 
#>  observations: 128 
#>  scales: 54 
#>  coherence range: 0.1246197 to 1.0000000
mwc
#> <awi_mwc>
#>  backend: biwavelet_transform_and_smoothing 0.20.22 
#>  observations: 128 
#>  scales: 54 
#>  coherence range: 0.9436774 to 1.0000000
```

The conditioning policy is stored with the result. Singular or highly
ill-conditioned local spectral matrices are therefore observable
scientific diagnostics rather than hidden numerical events.

Recent agricultural applications reinforce this distinction. Partial
wavelet coherence has been used to separate crop-yield responses to
meteorological and agricultural drought characteristics (Wang et al.
2026), while multiple wavelet coherence has been combined with
driver-screening strategies for cereal yield and climate oscillations
(Sankaran et al. 2026). These applications motivate the package, but do
not by themselves establish causal effects.

## Why significance is not the final design

The backend’s traditional AR(1) Monte Carlo significance is retained
only as a compatibility capability for the bivariate and legacy PWC
routes. Later releases will make null models explicit and treat the
time-scale plane as a multiple-testing field rather than a collection of
unrelated pointwise decisions.

## Reproducibility

Every wavelet result records the exact backend name and installed
version. This will become the basis for later reproducibility bundles,
validation tiers, and backend reconciliation.

## References

## Version 0.3.0 inferential layer

Version 0.3.0 adds explicit surrogate null models and field-aware
multiple-testing control. These operations are described in
`v04-null-models.Rmd` and `v05-field-aware-inference.Rmd`.

Grinsted, A., J. C. Moore, and S. Jevrejeva. 2004. “Application of the
Cross Wavelet Transform and Wavelet Coherence to Geophysical Time
Series.” *Nonlinear Processes in Geophysics* 11: 561–66.
<https://doi.org/10.5194/npg-11-561-2004>.

Hu, Wei, and Bing Si. 2021. “Technical Note: Improved Partial Wavelet
Coherency for Understanding Scale-Specific and Localized Bivariate
Relationships in Geosciences.” *Hydrology and Earth System Sciences* 25:
321–31. <https://doi.org/10.5194/hess-25-321-2021>.

Hu, Wei, and Bing Cheng Si. 2016. “Technical Note: Multiple Wavelet
Coherence for Untangling Scale-Specific and Localized Multivariate
Relationships in Geosciences.” *Hydrology and Earth System Sciences* 20:
3183–91. <https://doi.org/10.5194/hess-20-3183-2016>.

Liu, Yonggang, X. San Liang, and Robert H. Weisberg. 2007.
“Rectification of the Bias in the Wavelet Power Spectrum.” *Journal of
Atmospheric and Oceanic Technology* 24: 2093–102.
<https://doi.org/10.1175/2007JTECHO511.1>.

Sankaran, Adarsh, Fathima Fazal, Aleena Johnson, Venkataramana Sridhar,
Divyasree Prasad, and Aakhila Fathima Abdul Rasheed. 2026.
“Wavelet-Based Assessment of Climate Oscillation and Meteorological
Influences on Cereal Yield Variability Across India’s Agro-Climatic
Zones.” *Theoretical and Applied Climatology* 157: 147.
<https://doi.org/10.1007/s00704-026-06085-2>.

Torrence, Christopher, and Gilbert P. Compo. 1998. “A Practical Guide to
Wavelet Analysis.” *Bulletin of the American Meteorological Society* 79
(1): 61–78.
<https://doi.org/10.1175/1520-0477(1998)079%3C0061:APGTWA%3E2.0.CO;2>.

Veleda, Doris, Raul Montagne, and Moacyr Araujo. 2012. “Cross-Wavelet
Bias Corrected by Normalizing Scales.” *Journal of Atmospheric and
Oceanic Technology* 29: 1401–8.
<https://doi.org/10.1175/JTECH-D-11-00140.1>.

Wang, Hui, Xiaohan Lin, Peichao Gao, et al. 2026. “Divergent Responses
of Crop Yields to Meteorological and Agricultural Drought
Characteristics.” *Agricultural Water Management* 331: 110443.
<https://doi.org/10.1016/j.agwat.2026.110443>.
