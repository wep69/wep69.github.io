# Combine ensembles with the same units

Combine ensembles with the same units

## Usage

``` r
ensemble_combine(..., set_name = "set")
```

## Arguments

- ...:

  \`agri_uncertainty_ensemble\` objects, optionally named.

- set_name:

  Name of the design column identifying the source ensemble.

## Value

One \`agri_uncertainty_ensemble\`; designs are stacked and a
\`set_name\` column is added.

## Examples

``` r
e1 <- ensemble_from_scores(matrix(c(0.2, 0.3, 0.25, 0.4, 0.35, 0.3), 2), design = data.frame(year = factor(1:3)))
e2 <- ensemble_from_scores(matrix(c(0.7, 0.8, 0.75, 0.9, 0.85, 0.8), 2), design = data.frame(year = factor(1:3)))
ec <- ensemble_combine(baseline = e1, future = e2, set_name = "scenario")
uncertainty_decompose(ec)$shares
#>            year  scenario
#> [1,] 0.05857741 0.9414226
#> [2,] 0.03433476 0.9656652
```
