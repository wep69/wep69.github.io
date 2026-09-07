# Summarize wavelet coherence by phenological stage

Calculates stage-conditioned coherence summaries without replacing the
physical time scale by phenological time.

## Usage

``` r
awi_stage_coherence(x, stages, stage = NULL, period_range = NULL,
  metric = c("both", "awc", "posc"), significant = NULL,
  mask_coi = TRUE, weighting = c("area", "cell"),
  phenology_spec = attr(stages, "spec"), min_valid_fraction = 0.2)
```

## Arguments

- x:

  An `awi_wavelet_result` containing a coherence matrix.

- stages:

  An `awi_crop_stage` calendar.

- stage:

  One or more stage names. If omitted, all stages represented in the
  calendar are summarized.

- period_range:

  Optional two-element period band.

- metric:

  Return AWC, PoSC, or both.

- significant:

  Optional logical significance matrix or numeric threshold field. If
  omitted, the significance stored in `x` is used.

- mask_coi:

  Exclude cells outside the cone-of-influence validity region.

- weighting:

  Use time-period cell area or equal lattice-cell weights.

- phenology_spec:

  An `AwiPhenologySpec`.

- min_valid_fraction:

  Minimum valid fraction of the requested stage-period domain for
  eligibility.

## Value

An `awi_stage_coherence` object containing one row per requested stage
with numbers of time points and cells, valid fraction, AWC, PoSC, and
eligibility.

## Details

Stage membership is applied to the time columns of the original wavelet
field. AWC and PoSC are therefore summarized over selected physical
times and periods. A stage with insufficient observations or valid cells
is retained in the output but marked ineligible.
