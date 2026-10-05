# Attach or read an ensemble design

Attach or read an ensemble design

## Usage

``` r
ensemble_set_design(x, design)

ensemble_get_design(x)
```

## Arguments

- x:

  An \`agri_uncertainty_ensemble\`.

- design:

  Data frame with one row per member.

## Value

\`ensemble_set_design()\` returns the updated ensemble;
\`ensemble_get_design()\` the stored design or \`NULL\`.

## Examples

``` r
set.seed(9)
e <- ensemble_from_scores(matrix(runif(12), 2))
e <- ensemble_set_design(e, ensemble_design(year = 1:3, AWC = c(75, 150)))
ensemble_get_design(e)
#>   year AWC
#> 1    1  75
#> 2    2  75
#> 3    3  75
#> 4    1 150
#> 5    2 150
#> 6    3 150
```
