# Extract continuous suitability score

Extract continuous suitability score

## Usage

``` r
suitability_score(x)
```

## Arguments

- x:

  An \`agri_suitability\` object.

## Value

Continuous score object.

## Examples

``` r
s <- suit_aggregate(cbind(rain = c(0.9, 0.6, 0.3, 0.1), temp = c(1, 0.7, 0.8, 0.5)))
suitability_score(s)
#> [1] 0.9 0.6 0.3 0.1
```
