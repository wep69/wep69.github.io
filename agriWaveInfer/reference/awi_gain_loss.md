# Quantify incremental gain or loss in multiscale coherence summaries

Calculates an explicit incremental change between a baseline and
candidate coherence summary.

## Usage

``` r
awi_gain_loss(baseline, candidate, metric = c("posc", "awc"),
  period_range = NULL, time_range = NULL, mask_coi = TRUE,
  weighting = c("area", "cell"), min_gain = NULL)
```

## Arguments

- baseline:

  Baseline wavelet result or a matching `awi_driver_metric`.

- candidate:

  Candidate wavelet result or a matching `awi_driver_metric`.

- metric:

  `"posc"` or `"awc"`.

- period_range:

  Optional two-element period interval.

- time_range:

  Optional two-element time interval.

- mask_coi:

  Exclude cells outside the cone-of-influence validity region.

- weighting:

  Area or equal-cell weighting.

- min_gain:

  Retention threshold. Defaults to 5 percentage points for PoSC or 0.05
  for AWC.

## Value

An S3 `awi_gain_loss` object containing baseline, candidate, difference,
units, threshold and retention decision.

## Details

For PoSC, differences are percentage points, not relative percentages.
Gain-loss is a parsimony diagnostic and does not demonstrate that the
added predictor has an independent effect. Use conditional PWC or other
scientifically justified analyses to assess conditional support.
