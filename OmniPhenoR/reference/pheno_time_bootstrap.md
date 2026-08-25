# Bootstrap a longitudinal statistic by biological subject

Bootstrap a longitudinal statistic by biological subject

## Usage

``` r
pheno_time_bootstrap(data, subject, statistic, R = 200L, seed = 123)
```

## Arguments

- data:

  Long-form data frame.

- subject:

  Subject/plot identifier to resample as whole clusters.

- statistic:

  Function receiving a bootstrapped data frame and returning a numeric
  vector.

- R:

  Number of bootstrap replicates.

- seed:

  Random seed.

## Value

A list containing original estimate, bootstrap replicates and percentile
intervals.

## Examples

``` r
d <- subset(pheno_data("growth_series"),trait=="leaf_area")
stat <- function(z) c(mean=mean(z$value),max=max(z$value))
pheno_time_bootstrap(d,"plant_id",stat,R=20,seed=1)
#> $estimate
#>      mean       max 
#>  67.12714 124.00610 
#> 
#> $replicates
#>           mean      max
#>  [1,] 66.80145 120.5837
#>  [2,] 67.51888 124.0061
#>  [3,] 66.45182 124.0061
#>  [4,] 66.32630 124.0061
#>  [5,] 69.42207 124.0061
#>  [6,] 68.80982 124.0061
#>  [7,] 67.13369 124.0061
#>  [8,] 67.52038 124.0061
#>  [9,] 65.10910 120.3031
#> [10,] 68.29880 124.0061
#> [11,] 67.65174 124.0061
#> [12,] 67.19341 124.0061
#> [13,] 67.96343 120.5837
#> [14,] 65.05692 120.3031
#> [15,] 66.15935 119.4445
#> [16,] 67.75356 124.0061
#> [17,] 67.46836 124.0061
#> [18,] 67.20611 120.5837
#> [19,] 69.83011 124.0061
#> [20,] 65.94484 124.0061
#> 
#> $interval
#> # A tibble: 2 × 3
#>   statistic lower upper
#>   <chr>     <dbl> <dbl>
#> 1 mean       65.1  69.6
#> 2 max       120.  124. 
#> 
stat_auc <- function(z) {
  day_mean <- stats::aggregate(value ~ day, z, mean)
  c(auc = pheno_auc(day_mean, "day", "value"))
}
pheno_time_bootstrap(d, "plant_id", stat_auc, R = 10)
#> $estimate
#>      auc 
#> 1893.685 
#> 
#> $replicates
#>            auc
#>  [1,] 1871.358
#>  [2,] 1849.612
#>  [3,] 1859.494
#>  [4,] 1892.103
#>  [5,] 1843.181
#>  [6,] 1953.010
#>  [7,] 1925.444
#>  [8,] 1932.560
#>  [9,] 1869.053
#> [10,] 1942.488
#> 
#> $interval
#> # A tibble: 1 × 3
#>   statistic lower upper
#>   <chr>     <dbl> <dbl>
#> 1 auc       1845. 1951.
#> 
pheno_time_bootstrap(d,"plant_id",function(z)c(n=nrow(z)),R=5)
#> $estimate
#>  n 
#> 60 
#> 
#> $replicates
#>       n
#> [1,] 60
#> [2,] 60
#> [3,] 60
#> [4,] 60
#> [5,] 60
#> 
#> $interval
#> # A tibble: 1 × 3
#>   statistic lower upper
#>   <chr>     <dbl> <dbl>
#> 1 n            60    60
#> 
```
