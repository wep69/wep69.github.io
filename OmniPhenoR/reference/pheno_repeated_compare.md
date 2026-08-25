# Compare repeated-measures model candidates

Compare repeated-measures model candidates

## Usage

``` r
pheno_repeated_compare(...)
```

## Arguments

- ...:

  `pheno_repeated_fit` objects or underlying fitted model objects.

## Value

A tibble containing AIC, BIC and log-likelihood where available.

## Examples

``` r
d <- subset(pheno_data("growth_series"), trait == "leaf_area")
a <- pheno_repeated(d,"value","day","treatment","plant_id",engine="lm")
b <- pheno_repeated(d,"value","day",subject="plant_id",engine="lm")
pheno_repeated_compare(a,b)
#> # A tibble: 2 × 4
#>   model   AIC   BIC logLik
#>   <int> <dbl> <dbl>  <dbl>
#> 1     1  440.  473.  -204.
#> 2     2  443.  472.  -208.
```
