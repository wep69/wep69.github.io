# Suitable area and risk by scenario

Suitable area and risk by scenario

## Usage

``` r
scenario_risk_summary(
  scores,
  risk = NULL,
  area,
  baseline = "baseline",
  threshold = 0.5,
  common = TRUE
)
```

## Arguments

- scores:

  Named list of climatological score vectors (one per scenario, same
  units), including the baseline.

- risk:

  Optional named list of P(N) vectors.

- area:

  Unit areas (any unit).

- baseline:

  Name of the baseline element.

- threshold:

  Score defining suitable land (S1 + S2 by default).

- common:

  Restrict all scenarios to units valid in every scenario.

## Value

Data frame with \`id\`, \`suitable_area\`, \`change_pct\`, \`mean_PN\`
and \`dPN\`.

## Examples

``` r
scenario_risk_summary(scores = list(baseline = c(0.8, 0.6, 0.3, 0.55), plus2C = c(0.7, 0.45, 0.2, 0.5)),
                      risk = list(baseline = c(0.05, 0.2, 0.6, 0.3), plus2C = c(0.1, 0.35, 0.7, 0.4)),
                      area = c(25, 25, 30, 20))
#>         id n_units suitable_area mean_PN change_pct dPN
#> 1 baseline       4            70  0.2875    0.00000 0.0
#> 2   plus2C       4            45  0.3875  -35.71429 0.1
```
