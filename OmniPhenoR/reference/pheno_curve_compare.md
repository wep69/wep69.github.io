# Compare whole treatment trajectories using dynamic summaries

Compare whole treatment trajectories using dynamic summaries

## Usage

``` r
pheno_curve_compare(data, response, time, treatment, subject = NULL)
```

## Arguments

- data:

  Long-form data frame.

- response:

  Response column.

- time:

  Time column.

- treatment:

  Treatment column.

- subject:

  Optional subject column; if supplied, summaries are first computed per
  subject.

## Value

A tibble with AUC, peak, time-to-peak and maximum observed slope.

## Examples

``` r
d <- subset(pheno_data("growth_series"), trait == "leaf_area")
pheno_curve_compare(d,"value","day","treatment")
#> # A tibble: 3 × 5
#>   series     auc  peak time_peak max_slope
#>   <chr>    <dbl> <dbl>     <dbl>     <dbl>
#> 1 control  1907.  117.        28       Inf
#> 2 drought  1762.  108.        28       Inf
#> 3 nitrogen 2015.  124.        28       Inf
pheno_curve_compare(d,"value","day","treatment",subject="plant_id")
#> # A tibble: 12 × 5
#>    series         auc  peak time_peak max_slope
#>    <chr>        <dbl> <dbl>     <dbl>     <dbl>
#>  1 control.P01  1878.  117.        28      5.07
#>  2 control.P02  1878.  112.        28      5.53
#>  3 control.P03  1916.  115.        28      5.94
#>  4 control.P04  1921.  117.        28      5.59
#>  5 drought.P09  1754.  106.        28      4.92
#>  6 drought.P10  1774.  108.        28      5.34
#>  7 drought.P11  1719.  102.        28      5.47
#>  8 drought.P12  1768.  104.        28      5.47
#>  9 nitrogen.P05 1984.  121.        28      6.35
#> 10 nitrogen.P06 2051.  124.        28      5.80
#> 11 nitrogen.P07 2045.  120.        28      6.00
#> 12 nitrogen.P08 2036.  119.        28      6.16
pheno_curve_compare(subset(d,treatment=="control"),"value","day","treatment")
#> # A tibble: 1 × 5
#>   series    auc  peak time_peak max_slope
#>   <chr>   <dbl> <dbl>     <dbl>     <dbl>
#> 1 control 1907.  117.        28       Inf
```
