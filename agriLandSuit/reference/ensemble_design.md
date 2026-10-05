# Full-factorial ensemble design

Full-factorial ensemble design

## Usage

``` r
ensemble_design(...)
```

## Arguments

- ...:

  Named vectors of factor levels, for example \`year = 1992:2020\` and
  \`AWC = c(75, 100, 150)\`. The first factor varies fastest, as in
  \`expand.grid()\`.

## Value

Data frame of factors with one row per member.

## Examples

``` r
ensemble_design(year = 2001:2003, AWC = c(75, 100, 150))
#>   year AWC
#> 1 2001  75
#> 2 2002  75
#> 3 2003  75
#> 4 2001 100
#> 5 2002 100
#> 6 2003 100
#> 7 2001 150
#> 8 2002 150
#> 9 2003 150
```
