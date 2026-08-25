# Extract interpretable dynamic traits from fitted growth curves

Extract interpretable dynamic traits from fitted growth curves

## Usage

``` r
pheno_growth_traits(fit, grid_points = 201L, levels = c(0.25, 0.5, 0.75))
```

## Arguments

- fit:

  A `pheno_growth` object.

- grid_points:

  Number of prediction points per fitted series.

- levels:

  Relative levels used for crossing times, normally 0.25, 0.50, and
  0.75.

## Value

A tibble with minimum, maximum, AUC, maximum slope, inflection-time
proxy and requested crossing times.

## Examples

``` r
d <- subset(pheno_data("growth_series"), trait == "leaf_area" & plant_id %in% c("P01","P02"))
pheno_growth_traits(pheno_growth(d, "day", "value", "logistic", group = "plant_id"))
#> # A tibble: 2 × 11
#>   series status error minimum maximum   auc max_growth_rate time_max_growth
#>   <chr>  <chr>  <chr>   <dbl>   <dbl> <dbl>           <dbl>           <dbl>
#> 1 P01    ok     NA       13.3    118. 1876.            5.23            13.3
#> 2 P02    ok     NA       11.2    114. 1874.            5.61            12.3
#> # ℹ 3 more variables: t25 <dbl>, t50 <dbl>, t75 <dbl>
gs <- pheno_growth(d, "day", "value", "spline", group = "plant_id")
pheno_growth_traits(gs, grid_points = 101)
#> # A tibble: 2 × 11
#>   series status error minimum maximum   auc max_growth_rate time_max_growth
#>   <chr>  <chr>  <chr>   <dbl>   <dbl> <dbl>           <dbl>           <dbl>
#> 1 P01    ok     NA       14.5    117. 1883.            5.59            15.4
#> 2 P02    ok     NA       15.7    112. 1882.            6.20            13.2
#> # ℹ 3 more variables: t25 <dbl>, t50 <dbl>, t75 <dbl>
gq <- pheno_growth(d, "day", "value", "quadratic", group = "plant_id")
pheno_growth_traits(gq, levels = c(.1, .5, .9))
#> # A tibble: 2 × 11
#>   series status error minimum maximum   auc max_growth_rate time_max_growth
#>   <chr>  <chr>  <chr>   <dbl>   <dbl> <dbl>           <dbl>           <dbl>
#> 1 P01    ok     NA       11.3    120. 1879.            4.19            0.14
#> 2 P02    ok     NA       10.6    117. 1884.            4.51            0.14
#> # ℹ 3 more variables: t10 <dbl>, t50 <dbl>, t90 <dbl>
```
