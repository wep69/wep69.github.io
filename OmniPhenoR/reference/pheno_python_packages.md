# Inspect Python package/module availability

Inspect Python package/module availability

## Usage

``` r
pheno_python_packages(
  packages = c("plantcv", "ultralytics", "cv2"),
  initialize = FALSE
)
```

## Arguments

- packages:

  Python module names.

- initialize:

  Allow Python initialization.

## Value

A filtered table from
[`pheno_python_status()`](https://wep69.github.io/OmniPhenoR/reference/pheno_python_status.md).

## Examples

``` r
pheno_python_packages(c("plantcv", "ultralytics"))
#> # A tibble: 2 × 5
#>   capability  available version executable initialized
#>   <chr>       <lgl>     <chr>   <chr>      <lgl>      
#> 1 plantcv     NA        NA      NA         FALSE      
#> 2 ultralytics NA        NA      NA         FALSE      
pheno_python_packages("cv2", initialize = FALSE)
#> # A tibble: 1 × 5
#>   capability available version executable initialized
#>   <chr>      <lgl>     <chr>   <chr>      <lgl>      
#> 1 cv2        NA        NA      NA         FALSE      
pheno_python_packages(character())
#> # A tibble: 0 × 3
#> # ℹ 3 variables: capability <chr>, available <lgl>, version <chr>
```
