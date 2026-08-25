# Evaluate sensitivity to reduced acquisition frequency

Evaluate sensitivity to reduced acquisition frequency

## Usage

``` r
pheno_sampling_sensitivity(
  data,
  time,
  value,
  subject = NULL,
  every = c(2L, 3L, 4L),
  metric = c("auc", "maximum", "max_slope")
)
```

## Arguments

- data:

  Long-form data for one or more subject trajectories.

- time:

  Time column.

- value:

  Value column.

- subject:

  Optional subject identifier.

- every:

  Integer acquisition thinning factors.

- metric:

  `"auc"`, `"maximum"`, or `"max_slope"`.

## Value

A tibble comparing full and thinned estimates.

## Examples

``` r
d <- subset(pheno_data("growth_series"),trait=="leaf_area")
pheno_sampling_sensitivity(d,"day","value","plant_id",every=c(2,3),metric="auc")
#> # A tibble: 24 × 6
#>    series every metric  full thinned relative_error
#>    <chr>  <dbl> <chr>  <dbl>   <dbl>          <dbl>
#>  1 P01        2 auc    1878.   1851.       -0.0147 
#>  2 P01        3 auc    1878.   1224.       -0.348  
#>  3 P02        2 auc    1878.   1868.       -0.00536
#>  4 P02        3 auc    1878.   1259.       -0.329  
#>  5 P03        2 auc    1916.   1929.        0.00685
#>  6 P03        3 auc    1916.   1249.       -0.348  
#>  7 P04        2 auc    1921.   1944.        0.0120 
#>  8 P04        3 auc    1921.   1293.       -0.327  
#>  9 P05        2 auc    1984.   1999.        0.00727
#> 10 P05        3 auc    1984.   1328.       -0.331  
#> # ℹ 14 more rows
pheno_sampling_sensitivity(d,"day","value","plant_id",every=2,metric="maximum")
#> # A tibble: 12 × 6
#>    series every metric   full thinned relative_error
#>    <chr>  <dbl> <chr>   <dbl>   <dbl>          <dbl>
#>  1 P01        2 maximum  117.    117.              0
#>  2 P02        2 maximum  112.    112.              0
#>  3 P03        2 maximum  115.    115.              0
#>  4 P04        2 maximum  117.    117.              0
#>  5 P05        2 maximum  121.    121.              0
#>  6 P06        2 maximum  124.    124.              0
#>  7 P07        2 maximum  120.    120.              0
#>  8 P08        2 maximum  119.    119.              0
#>  9 P09        2 maximum  106.    106.              0
#> 10 P10        2 maximum  108.    108.              0
#> 11 P11        2 maximum  102.    102.              0
#> 12 P12        2 maximum  104.    104.              0
pheno_sampling_sensitivity(subset(d,plant_id=="P01"),"day","value",
  every=c(2,4),metric="max_slope")
#> # A tibble: 2 × 6
#>   series every metric     full thinned relative_error
#>   <chr>  <dbl> <chr>     <dbl>   <dbl>          <dbl>
#> 1 series     2 max_slope  5.07    3.73         -0.264
#> 2 series     4 max_slope  5.07    3.65         -0.280
```
