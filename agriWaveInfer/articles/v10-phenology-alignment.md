# Phenology-Aligned Multiscale Inference

## Why phenology should be explicit

Calendar months are not equivalent to crop development. The same
climatic anomaly may occur during vegetative growth in one season and
during flowering in another. Phenological variability is itself
associated with yield variability, so crop-stage context should be
represented explicitly rather than inferred after the analysis (Singh et
al. 2026).

Version 0.6.0 introduces a stage calendar that preserves the original
physical time coordinate.

## Define a validated stage policy

``` r

ps <- awi_phenology_spec(
  stage_order = c("emergence", "vegetative", "flowering", "grain_filling", "maturity"),
  boundary = "left_closed_right_open",
  min_stage_observations = 3,
  preserve_physical_time = TRUE
)
```

Physical time preservation is mandatory in this release. A normalized
phenological clock may be useful later, but it changes the meaning of a
wavelet period and therefore requires separate validation.

## Create a crop-stage calendar

``` r

stages <- awi_crop_stage(
  stage = c("emergence", "vegetative", "flowering", "grain_filling", "maturity"),
  start = c(1, 16, 46, 61, 96),
  end = c(16, 46, 61, 96, 120),
  cycle = 1,
  crop = "maize",
  spec = ps
)
```

The calendar stores crop, cultivar, source and boundary uncertainty.
Overlapping intervals are rejected by default.

## Align a measured series

``` r

a <- awi_stage_align(ndvi, stages)
head(a)
attr(a, "stage_counts")
```

The original observation time is retained. `stage_fraction` only records
position within a stage and is not substituted for the physical time
axis.

## Summarize coherence by stage

``` r

w <- awi_wtc(vpd, ndvi, significance = TRUE, nrands = 199)
s <- awi_stage_coherence(
  w,
  stages,
  period_range = c(8, 45),
  metric = "both"
)
s$table
```

The output reports stage-specific AWC and PoSC, the number of
represented time points, valid field coverage and eligibility. This
makes it possible to report, for example, that a VPD-NDVI relationship
was concentrated during flowering and grain filling rather than
describing only a seasonal average.

## Interpretation

Stage-conditioned coherence is still an association. It can improve
agronomic interpretation because the exposure window is biologically
meaningful, but it does not by itself identify a physiological mechanism
or causal effect.

Singh, Pashupati Nath, Shobhit Pipil, Prashant K. Srivastava, and
Shailendra Kumar Verma. 2026. “Phenological Coherence and Yield
Variability of Rabi Crops Under Short-Term Climatic and
Socio-Environmental Disturbance in Eastern Uttar Pradesh.” *Field Crops
Research* 342: 110472. <https://doi.org/10.1016/j.fcr.2026.110472>.
