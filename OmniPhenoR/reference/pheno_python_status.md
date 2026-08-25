# Inspect the optional Python environment

The default does not initialize Python. This keeps package loading and
CRAN checks independent of Python. Set `initialize = TRUE` only when
Python-backed capabilities are deliberately being inspected or used.

## Usage

``` r
pheno_python_status(
  initialize = FALSE,
  packages = c("plantcv", "ultralytics", "cv2", "torch")
)
```

## Arguments

- initialize:

  Initialize Python through `reticulate`.

- packages:

  Python modules to inspect when initialization is allowed.

## Value

A tibble with Python and module availability information.

## Examples

``` r
pheno_python_status()
#> # A tibble: 6 × 5
#>   capability  available version executable initialized
#>   <chr>       <lgl>     <chr>   <chr>      <lgl>      
#> 1 reticulate  TRUE      1.46.0  NA         FALSE      
#> 2 python      FALSE     NA      NA         FALSE      
#> 3 plantcv     NA        NA      NA         FALSE      
#> 4 ultralytics NA        NA      NA         FALSE      
#> 5 cv2         NA        NA      NA         FALSE      
#> 6 torch       NA        NA      NA         FALSE      
pheno_python_status(packages = c("plantcv", "ultralytics"))
#> # A tibble: 4 × 5
#>   capability  available version executable initialized
#>   <chr>       <lgl>     <chr>   <chr>      <lgl>      
#> 1 reticulate  TRUE      1.46.0  NA         FALSE      
#> 2 python      FALSE     NA      NA         FALSE      
#> 3 plantcv     NA        NA      NA         FALSE      
#> 4 ultralytics NA        NA      NA         FALSE      
if (requireNamespace("reticulate", quietly = TRUE)) pheno_python_status(FALSE)
#> # A tibble: 6 × 5
#>   capability  available version executable initialized
#>   <chr>       <lgl>     <chr>   <chr>      <lgl>      
#> 1 reticulate  TRUE      1.46.0  NA         FALSE      
#> 2 python      FALSE     NA      NA         FALSE      
#> 3 plantcv     NA        NA      NA         FALSE      
#> 4 ultralytics NA        NA      NA         FALSE      
#> 5 cv2         NA        NA      NA         FALSE      
#> 6 torch       NA        NA      NA         FALSE      
```
