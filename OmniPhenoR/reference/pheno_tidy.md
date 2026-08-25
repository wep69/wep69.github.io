# Convert dynamic analyses to one-row-per-subject trait tables

Convert dynamic analyses to one-row-per-subject trait tables

## Usage

``` r
pheno_tidy(data, subject, time, value, trait = NULL)
```

## Arguments

- data:

  Long-form data frame.

- subject:

  Subject identifier.

- time:

  Time column.

- value:

  Value column.

- trait:

  Optional trait column. If supplied, summaries are generated for each
  subject-trait combination and widened into prefixed columns.

## Value

A tidy subject-level phenotype table.

## Examples

``` r
d <- pheno_data("growth_series")
pheno_tidy(d,"plant_id","day","value","trait")
#> # A tibble: 12 × 7
#>    subject green_fraction_auc green_fraction_maximum green_fraction_max_slope
#>    <chr>                <dbl>                  <dbl>                    <dbl>
#>  1 P01                   23.9                  0.884                0.000394 
#>  2 P02                   24.0                  0.895                0.00299  
#>  3 P03                   24.0                  0.887                0.00150  
#>  4 P04                   24.3                  0.903                0.00306  
#>  5 P05                   23.9                  0.887                0.00179  
#>  6 P06                   24.1                  0.894                0.00359  
#>  7 P07                   24.2                  0.894                0.00201  
#>  8 P08                   23.9                  0.887                0.00135  
#>  9 P09                   23.5                  0.889                0.00151  
#> 10 P10                   23.6                  0.893                0.000353 
#> 11 P11                   23.7                  0.891                0.00291  
#> 12 P12                   23.7                  0.893                0.0000690
#> # ℹ 3 more variables: leaf_area_auc <dbl>, leaf_area_maximum <dbl>,
#> #   leaf_area_max_slope <dbl>
pheno_tidy(subset(d,trait=="leaf_area"),"plant_id","day","value")
#> # A tibble: 12 × 5
#>    subject   auc minimum maximum max_slope
#>    <chr>   <dbl>   <dbl>   <dbl>     <dbl>
#>  1 P01     1878.    14.5    117.      5.07
#>  2 P02     1878.    15.7    112.      5.53
#>  3 P03     1916.    14.6    115.      5.94
#>  4 P04     1921.    19.2    117.      5.59
#>  5 P05     1984.    15.9    121.      6.35
#>  6 P06     2051.    16.5    124.      5.80
#>  7 P07     2045.    16.4    120.      6.00
#>  8 P08     2036.    17.7    119.      6.16
#>  9 P09     1754.    18.6    106.      4.92
#> 10 P10     1774.    14.9    108.      5.34
#> 11 P11     1719.    15.7    102.      5.47
#> 12 P12     1768.    17.0    104.      5.47
head(pheno_tidy(d,"plant_id","day","value","trait"),3)
#> # A tibble: 3 × 7
#>   subject green_fraction_auc green_fraction_maximum green_fraction_max_slope
#>   <chr>                <dbl>                  <dbl>                    <dbl>
#> 1 P01                   23.9                  0.884                 0.000394
#> 2 P02                   24.0                  0.895                 0.00299 
#> 3 P03                   24.0                  0.887                 0.00150 
#> # ℹ 3 more variables: leaf_area_auc <dbl>, leaf_area_maximum <dbl>,
#> #   leaf_area_max_slope <dbl>
```
