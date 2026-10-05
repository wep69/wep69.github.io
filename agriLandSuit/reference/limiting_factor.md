# Identify the limiting criterion

Identify the limiting criterion

## Usage

``` r
limiting_factor(
  x,
  na_policy = c("propagate", "available"),
  tolerance = 1e-12,
  none_threshold = NULL
)
```

## Arguments

- x:

  Supported criterion-score input.

- na_policy:

  Missing-data policy, as in \`suit_aggregate()\`.

- tolerance:

  Non-negative tolerance used to count ties around the minimum.

- none_threshold:

  Optional score in (0, 1\]. Units whose minimum score is at or above
  this value have no limiting criterion and receive index 0, labelled
  \`none\` in the key. \`NULL\` (default) keeps the 1.0.0 behaviour.

## Value

An \`agri_limiting_factor\` object with minimum score, first limiting
criterion index, tie count, and criterion key.

## Examples

``` r
z <- matrix(c(1, 0.4, 1, 0.9, 0.6, 1), ncol = 2, dimnames = list(NULL, c("rain", "temperature")))
limiting_factor(z)$index
#> [1] 2 1 1
lf <- limiting_factor(z, none_threshold = 0.999)
lf$index
#> [1] 2 1 0
lf$key
#>   index   criterion
#> 1     0        none
#> 2     1        rain
#> 3     2 temperature
```
