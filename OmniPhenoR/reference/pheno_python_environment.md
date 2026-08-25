# Summarize the Python environment policy

Summarize the Python environment policy

## Usage

``` r
pheno_python_environment(initialize = FALSE)
```

## Arguments

- initialize:

  Allow Python initialization.

## Value

A named list describing environment policy and status.

## Examples

``` r
pheno_python_environment()
#> $policy
#> [1] "optional; no Python initialization or installation during package load/check"
#> 
#> $status
#> # A tibble: 6 × 5
#>   capability  available version executable initialized
#>   <chr>       <lgl>     <chr>   <chr>      <lgl>      
#> 1 reticulate  TRUE      1.46.0  NA         FALSE      
#> 2 python      FALSE     NA      NA         FALSE      
#> 3 plantcv     NA        NA      NA         FALSE      
#> 4 ultralytics NA        NA      NA         FALSE      
#> 5 cv2         NA        NA      NA         FALSE      
#> 6 torch       NA        NA      NA         FALSE      
#> 
#> $requirements
#> $requirements$plantcv
#> $requirements$plantcv$packages
#> [1] "plantcv>=4.11,<5"
#> 
#> $requirements$plantcv$python
#> [1] ">=3.11,<3.14"
#> 
#> 
#> $requirements$yolo
#> $requirements$yolo$packages
#> [1] "ultralytics>=8"
#> 
#> $requirements$yolo$python
#> [1] ">=3.8"
#> 
#> 
#> $requirements$ultralytics
#> $requirements$ultralytics$packages
#> [1] "ultralytics>=8"
#> 
#> $requirements$ultralytics$python
#> [1] ">=3.8"
#> 
#> 
#> $requirements$opencv
#> $requirements$opencv$packages
#> [1] "opencv-python>=4"
#> 
#> $requirements$opencv$python
#> [1] ">=3.8"
#> 
#> 
#> $requirements$generic
#> $requirements$generic$packages
#> character(0)
#> 
#> $requirements$generic$python
#> NULL
#> 
#> 
#> 
#> $managed_environment_guidance
#> [1] "Use reticulate::py_require() explicitly when a managed environment is desired."
#> 
pheno_python_environment(FALSE)$policy
#> [1] "optional; no Python initialization or installation during package load/check"
names(pheno_python_environment())
#> [1] "policy"                       "status"                      
#> [3] "requirements"                 "managed_environment_guidance"
```
