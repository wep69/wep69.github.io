# Aggregate object-level traits to biological units

Aggregate object-level traits to biological units

## Usage

``` r
pheno_aggregate_traits(
  data,
  by,
  traits = NULL,
  functions = c("mean", "median", "sum", "sd")
)
```

## Arguments

- data:

  Data frame or canonical object result.

- by:

  Grouping columns.

- traits:

  Numeric traits; defaults to all numeric columns except obvious
  identifiers.

- functions:

  Summary functions among `mean`, `median`, `sum`, `sd`, `min`, `max`.

## Value

Wide summary tibble including object count.

## Examples

``` r
z<-data.frame(plant_id=c("a","a","b"),area=c(10,12,20),severity=c(2,4,5))
pheno_aggregate_traits(z,"plant_id")
#> # A tibble: 2 × 10
#>   plant_id n_objects area_mean area_median area_sum area_sd severity_mean
#>   <chr>        <int>     <dbl>       <dbl>    <dbl>   <dbl>         <dbl>
#> 1 a                2        11          11       22    1.41             3
#> 2 b                1        20          20       20   NA                5
#> # ℹ 3 more variables: severity_median <dbl>, severity_sum <dbl>,
#> #   severity_sd <dbl>
pheno_aggregate_traits(z,"plant_id",traits="area",functions=c("mean","sum"))
#> # A tibble: 2 × 4
#>   plant_id n_objects area_mean area_sum
#>   <chr>        <int>     <dbl>    <dbl>
#> 1 a                2        11       22
#> 2 b                1        20       20
pheno_aggregate_traits(transform(z, severity = NA_real_), "plant_id", traits = "severity")
#> # A tibble: 2 × 6
#>   plant_id n_objects severity_mean severity_median severity_sum severity_sd
#>   <chr>        <int>         <dbl>           <dbl>        <dbl>       <dbl>
#> 1 a                2            NA              NA           NA          NA
#> 2 b                1            NA              NA           NA          NA
```
