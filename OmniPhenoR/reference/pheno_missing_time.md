# Inspect missing longitudinal values

Inspect missing longitudinal values

## Usage

``` r
pheno_missing_time(x)
```

## Arguments

- x:

  `pheno_series`.

## Value

Tibble.

## Examples

``` r
d<-data.frame(plant=1,day=1:3,trait="a",value=c(1,NA,3))
s<-pheno_series(d,"plant","day","trait","value")
pheno_missing_time(s)
#> # A tibble: 1 × 3
#>     row  time reason       
#>   <int> <int> <chr>        
#> 1     2     2 missing_value
pheno_missing_time(pheno_series(transform(d,value=c(NA,NA,3)),"plant","day","trait","value"))
#> # A tibble: 2 × 3
#>     row  time reason       
#>   <int> <int> <chr>        
#> 1     1     1 missing_value
#> 2     2     2 missing_value
nrow(pheno_missing_time(s))
#> [1] 1
```
