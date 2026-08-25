# Correlate longitudinal phenotypes while preserving subject structure

Correlate longitudinal phenotypes while preserving subject structure

## Usage

``` r
pheno_time_cor(
  data,
  id,
  time,
  trait,
  value,
  trait_x,
  trait_y,
  lag = 0,
  within = TRUE,
  method = c("pearson", "spearman", "kendall")
)
```

## Arguments

- data:

  Long-form data frame.

- id:

  Subject identifier.

- time:

  Time column.

- trait:

  Trait column.

- value:

  Numeric value column.

- trait_x:

  First trait.

- trait_y:

  Second trait.

- lag:

  Time lag applied to the second trait.

- within:

  Center values within subjects before correlation.

- method:

  Correlation method.

## Value

A one-row tibble with correlation and matched sample size.

## Examples

``` r
d <- pheno_data("growth_series")
pheno_time_cor(d,"plant_id","day","trait","value","leaf_area","green_fraction")
#> # A tibble: 1 × 7
#>   trait_x   trait_y          lag within     n correlation method 
#>   <chr>     <chr>          <dbl> <lgl>  <int>       <dbl> <chr>  
#> 1 leaf_area green_fraction     0 TRUE      60      -0.565 pearson
pheno_time_cor(d,"plant_id","day","trait","value","leaf_area","green_fraction",lag=7)
#> # A tibble: 1 × 7
#>   trait_x   trait_y          lag within     n correlation method 
#>   <chr>     <chr>          <dbl> <lgl>  <int>       <dbl> <chr>  
#> 1 leaf_area green_fraction     7 TRUE      48      -0.766 pearson
pheno_time_cor(
  d, "plant_id", "day", "trait", "value",
  "leaf_area", "green_fraction", within = FALSE, method = "spearman"
)
#> # A tibble: 1 × 7
#>   trait_x   trait_y          lag within     n correlation method  
#>   <chr>     <chr>          <dbl> <lgl>  <int>       <dbl> <chr>   
#> 1 leaf_area green_fraction     0 FALSE     60      -0.363 spearman
```
