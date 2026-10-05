# Evaluate AHP matrix consistency

Evaluate AHP matrix consistency

## Usage

``` r
ahp_consistency(x, threshold = 0.1)
```

## Arguments

- x:

  A complete positive reciprocal matrix.

- threshold:

  Consistency-ratio threshold, commonly 0.10.

## Value

An \`agri_ahp_consistency\` object containing lambda_max, CI, RI and CR.

## Examples

``` r
cmp <- data.frame(criterion1 = c("climate", "climate", "soil"),
                  criterion2 = c("soil", "terrain", "terrain"), value = c(2, 5, 3))
ahp_consistency(ahp_matrix(c("climate", "soil", "terrain"), cmp))
#> <agri_ahp_consistency> CR: 0.003185  threshold: 0.1  acceptable: TRUE 
```
