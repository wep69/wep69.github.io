# Summarize area or cell counts for suitability transitions

Summarize area or cell counts for suitability transitions

## Usage

``` r
scenario_area_change(
  x,
  baseline = NULL,
  scenario = NULL,
  unit = c("ha", "km2", "m2", "cells")
)
```

## Arguments

- x:

  An \`agri_scenario_transition\` object or an
  \`agri_scenario_suitability\` object.

- baseline, scenario:

  Passed to \`scenario_transition()\` when needed.

- unit:

  One of \`cells\`, \`ha\`, \`km2\`, or \`m2\`.

## Value

A data.frame of class transitions with class change and area/count,
inheriting from \`agri_scenario_area\`.
