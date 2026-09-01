# Compute suitability change relative to a baseline

Compute suitability change relative to a baseline

## Usage

``` r
scenario_delta(
  x,
  baseline = NULL,
  scenario = NULL,
  type = c("absolute", "relative"),
  epsilon = 1e-12
)
```

## Arguments

- x:

  An \`agri_scenario_suitability\` object.

- baseline:

  Optional baseline scenario ID; defaults to the set baseline.

- scenario:

  Optional comparison scenario IDs; defaults to all non-baseline
  scenarios.

- type:

  \`absolute\` for future minus baseline, or \`relative\` for the
  difference divided by the absolute baseline score.

- epsilon:

  Baseline magnitudes at or below epsilon become NA for relative change.

## Value

An \`agri_scenario_delta\` object.
