# Validate an optional Python backend without installing it

Validate an optional Python backend without installing it

## Usage

``` r
pheno_python_validate(
  backend = c("plantcv", "yolo", "ultralytics", "opencv"),
  initialize = FALSE
)
```

## Arguments

- backend:

  Backend identifier.

- initialize:

  Allow Python initialization.

## Value

A one-row tibble.

## Examples

``` r
pheno_python_validate("plantcv")
#> # A tibble: 1 × 6
#>   backend module  available version initialized note                            
#>   <chr>   <chr>   <lgl>     <chr>   <lgl>       <chr>                           
#> 1 plantcv plantcv NA        NA      FALSE       Python not initialized; module …
pheno_python_validate("yolo", initialize = FALSE)
#> # A tibble: 1 × 6
#>   backend module      available version initialized note                        
#>   <chr>   <chr>       <lgl>     <chr>   <lgl>       <chr>                       
#> 1 yolo    ultralytics NA        NA      FALSE       Python not initialized; mod…
pheno_python_validate("opencv")
#> # A tibble: 1 × 6
#>   backend module available version initialized note                             
#>   <chr>   <chr>  <lgl>     <chr>   <lgl>       <chr>                            
#> 1 opencv  cv2    NA        NA      FALSE       Python not initialized; module s…
```
