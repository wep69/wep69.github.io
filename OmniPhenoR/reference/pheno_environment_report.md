# Write a reproducibility-oriented environment report

Write a reproducibility-oriented environment report

## Usage

``` r
pheno_environment_report(path = NULL, initialize_python = FALSE)
```

## Arguments

- path:

  Optional Markdown output path.

- initialize_python:

  Allow Python initialization.

## Value

A named list invisibly when `path` is supplied, visibly otherwise.

## Examples

``` r
pheno_environment_report()
#> $timestamp
#> [1] "2026-08-25 01:02:12 UTC"
#> 
#> $R
#> [1] "R version 4.6.0 (2026-04-24 ucrt)"
#> 
#> $platform
#> [1] "x86_64-w64-mingw32"
#> 
#> $OmniPhenoR
#> [1] "1.0.0"
#> 
#> $python
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
#> $capabilities
#> # A tibble: 40 × 5
#>    capability     engine            package     available status   
#>    <chr>          <chr>             <chr>       <lgl>     <chr>    
#>  1 rgb_indices    native            NA          TRUE      available
#>  2 segmentation   native/torch      NA          TRUE      available
#>  3 morphology     native            NA          TRUE      available
#>  4 texture        native            NA          TRUE      available
#>  5 plant_image    pliman            pliman      TRUE      available
#>  6 orthomosaic    FIELDimageR       FIELDimageR TRUE      available
#>  7 spatial_vector sf/terra          terra       TRUE      available
#>  8 gabor          native/OpenImageR OpenImageR  TRUE      available
#>  9 wavelet        native/waveslim   waveslim    TRUE      available
#> 10 deep_learning  torch             torch       TRUE      available
#> # ℹ 30 more rows
#> 
f <- tempfile(fileext = ".md"); pheno_environment_report(f); file.exists(f)
#> [1] TRUE
unlink(f)
```
