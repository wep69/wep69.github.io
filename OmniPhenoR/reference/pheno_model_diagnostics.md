# Diagnose a fitted temporal model

Diagnose a fitted temporal model

## Usage

``` r
pheno_model_diagnostics(model)
```

## Arguments

- model:

  `pheno_repeated_fit`, `pheno_growth`, or standard fitted model.

## Value

A list of residual diagnostics or growth-fit status.

## Examples

``` r
d <- subset(pheno_data("growth_series"), trait == "leaf_area")
m <- pheno_repeated(d,"value","day","treatment","plant_id",engine="lm")
pheno_model_diagnostics(m)
#> $summary
#> # A tibble: 1 × 4
#>       n mean_residual sd_residual cor_fitted_residual
#>   <int>         <dbl>       <dbl>               <dbl>
#> 1    60     -2.97e-17        7.29            3.70e-17
#> 
#> $residuals
#> # A tibble: 60 × 2
#>    fitted residual
#>     <dbl>    <dbl>
#>  1   12.8    1.74 
#>  2   39.8   -5.76 
#>  3   66.8   -0.104
#>  4   93.8    8.35 
#>  5  121.    -4.22 
#>  6   12.4    3.26 
#>  7   39.5   -8.83 
#>  8   66.5    2.87 
#>  9   93.5   10.7  
#> 10  120.    -8.05 
#> # ℹ 50 more rows
#> 
pheno_model_diagnostics(pheno_growth(subset(d,plant_id=="P01"),"day","value","linear"))
#> # A tibble: 1 × 2
#>   series ok   
#>   <chr>  <lgl>
#> 1 series TRUE 
```
