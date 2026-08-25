# Detect gaps in a canonical longitudinal series

Detect gaps in a canonical longitudinal series

## Usage

``` r
pheno_time_gaps(x, expected = NULL, multiplier = 1.5)
```

## Arguments

- x:

  `pheno_series`.

- expected:

  Expected step; median positive step if omitted.

- multiplier:

  Gap threshold.

## Value

Tibble.

## Examples

``` r
s<-pheno_series(data.frame(plant=1,day=c(0,1,5),trait="a",value=1:3),
  "plant","day","trait","value")
pheno_time_gaps(s)
#> # A tibble: 1 × 5
#>   group row_before row_after   gap expected
#>   <chr>      <int>     <int> <dbl>    <dbl>
#> 1 1.a            2         3     4      2.5
pheno_time_gaps(s,expected=1)
#> # A tibble: 1 × 5
#>   group row_before row_after   gap expected
#>   <chr>      <int>     <int> <dbl>    <dbl>
#> 1 1.a            2         3     4        1
pheno_time_gaps(s,expected=2,multiplier=1.5)
#> # A tibble: 1 × 5
#>   group row_before row_after   gap expected
#>   <chr>      <int>     <int> <dbl>    <dbl>
#> 1 1.a            2         3     4        2
```
