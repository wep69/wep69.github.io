# Cluster longitudinal trajectories

Cluster longitudinal trajectories

## Usage

``` r
pheno_trajectory_cluster(
  x,
  centers = 2L,
  representation = c("fpca", "matrix"),
  ncomp = 3L,
  seed = 123
)
```

## Arguments

- x:

  A `pheno_functional` or `pheno_fpca` object.

- centers:

  Number of clusters.

- representation:

  `"fpca"` or `"matrix"`.

- ncomp:

  Number of FPCA scores used when representation is `"fpca"`.

- seed:

  Random seed for k-means.

## Value

A tibble of subject cluster assignments.

## Examples

``` r
d <- subset(pheno_data("growth_series"), trait == "leaf_area")
f <- pheno_functional(d,"plant_id","day","value")
pheno_trajectory_cluster(f,centers=2)
#> # A tibble: 12 × 2
#>    id    cluster
#>    <chr>   <int>
#>  1 P01         1
#>  2 P02         1
#>  3 P03         1
#>  4 P04         1
#>  5 P05         1
#>  6 P06         1
#>  7 P07         1
#>  8 P08         1
#>  9 P09         2
#> 10 P10         2
#> 11 P11         2
#> 12 P12         2
pheno_trajectory_cluster(f,centers=3,ncomp=2,seed=9)
#> # A tibble: 12 × 2
#>    id    cluster
#>    <chr>   <int>
#>  1 P01         2
#>  2 P02         2
#>  3 P03         2
#>  4 P04         2
#>  5 P05         3
#>  6 P06         3
#>  7 P07         3
#>  8 P08         3
#>  9 P09         1
#> 10 P10         1
#> 11 P11         1
#> 12 P12         1
pheno_trajectory_cluster(pheno_fpca(f,2),centers=2)
#> # A tibble: 12 × 2
#>    id    cluster
#>    <chr>   <int>
#>  1 P01         1
#>  2 P02         1
#>  3 P03         1
#>  4 P04         1
#>  5 P05         1
#>  6 P06         1
#>  7 P07         1
#>  8 P08         1
#>  9 P09         2
#> 10 P10         2
#> 11 P11         2
#> 12 P12         2
```
