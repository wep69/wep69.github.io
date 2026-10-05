# Scenario ensemble from yearly scores of several scenarios

Stacks yearly member scores of several climate scenarios (for example
GCM x SSP x period) into one ensemble whose members are scenario-year
pairs. The design carries the scenario descriptors plus \`year\`, so
\`uncertainty_decompose()\` can separate model, pathway, period and
interannual variability.

## Usage

``` r
scenario_ensemble(
  scores,
  design,
  years = NULL,
  breaks = c(0, 0.25, 0.5, 0.75, 1),
  labels = c("N", "S3", "S2", "S1"),
  units = NULL
)
```

## Arguments

- scores:

  Named list of matrices (units x years), one per scenario.

- design:

  Data frame with one row per scenario and a column \`id\` matching
  \`names(scores)\`; other columns are scenario descriptors.

- years:

  Year labels; default the column names of the first matrix.

- breaks, labels:

  Class definition stored with the ensemble.

- units:

  Optional unit (row) labels.

## Value

An \`agri_uncertainty_ensemble\`.

## Examples

``` r
set.seed(1)
sc <- list(g1_245 = matrix(runif(6), 2), g2_245 = matrix(runif(6), 2), g1_585 = matrix(runif(6), 2), g2_585 = matrix(runif(6), 2))
des <- data.frame(id = names(sc), GCM = c("g1", "g2", "g1", "g2"), SSP = c("245", "245", "585", "585"))
e <- scenario_ensemble(sc, des, years = 2001:2003)
ensemble_get_design(e)[1:4, ]
#>   GCM SSP year
#> 1  g1 245 2001
#> 2  g1 245 2002
#> 3  g1 245 2003
#> 4  g2 245 2001
uncertainty_decompose(e)$shares
#>             GCM          SSP       year
#> [1,] 0.03120838 0.1926999352 0.21223385
#> [2,] 0.28391182 0.0005414933 0.03605815
```
