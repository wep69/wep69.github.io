# Specify phenology alignment rules

Creates a validated policy for mapping physical observations to crop
stages without silently replacing days, months, or index units by a
normalized phenological clock.

## Usage

``` r
awi_phenology_spec(
  stage_order = c("emergence", "vegetative", "flowering", "grain_filling", "maturity"),
  boundary = c("left_closed_right_open", "closed"),
  overlap_policy = c("error", "first"), min_stage_observations = 3L,
  preserve_physical_time = TRUE, uncertainty_tolerance = 0
)
```

## Arguments

- stage_order:

  Ordered unique names of phenological stages.

- boundary:

  Interval-boundary convention used when assigning observations to
  stages.

- overlap_policy:

  How overlapping intervals are handled. The default raises an error.

- min_stage_observations:

  Minimum number of observations required for a stage to be considered
  eligible in summaries.

- preserve_physical_time:

  Must be `TRUE` in version 0.6.0. Stage annotation does not warp the
  physical time axis.

- uncertainty_tolerance:

  Additional non-negative boundary uncertainty in the native time units.

## Value

An S7 object of class `AwiPhenologySpec`.

## Details

Version 0.6.0 deliberately supports annotation and stage-conditioned
summaries while preserving physical time. Phenological time warping
remains outside the validated API because it changes the interpretation
of wavelet periods.

## References

Singh PN, Pipil S, Srivastava PK, Verma SK (2026). Phenological
coherence and yield variability of Rabi crops under short-term climatic
and socio-environmental disturbance in eastern Uttar Pradesh. Field
Crops Research 342:110472. doi:10.1016/j.fcr.2026.110472.
