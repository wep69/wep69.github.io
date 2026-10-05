# Construct a complete reciprocal AHP matrix

Construct a complete reciprocal AHP matrix

## Usage

``` r
ahp_matrix(criteria, comparisons)
```

## Arguments

- criteria:

  Unique criterion names.

- comparisons:

  Data frame with columns \`criterion1\`, \`criterion2\`, and \`value\`.
  Values express how many times criterion1 is preferred to criterion2.

## Value

An \`agri_ahp_matrix\` object.

## Examples

``` r
cmp <- data.frame(criterion1 = c("climate", "climate", "soil"),
                  criterion2 = c("soil", "terrain", "terrain"), value = c(2, 5, 3))
m <- ahp_matrix(c("climate", "soil", "terrain"), cmp)
m
#> <agri_ahp_matrix> 3 criteria
#>         climate      soil terrain
#> climate     1.0 2.0000000       5
#> soil        0.5 1.0000000       3
#> terrain     0.2 0.3333333       1
```
