# Audit an OmniPhenoR object or workflow result

Collects package version, R version, capability status, settings, and
optional SHA-256 hashes for file inputs. Audit records are intended to
travel with frozen phenotyping outputs.

## Usage

``` r
pheno_audit(x = NULL, files = NULL)
```

## Arguments

- x:

  Optional object to audit.

- files:

  Optional existing files to hash.

## Value

A named list.

## Examples

``` r
pheno_audit()
#> $timestamp
#> [1] "2026-08-25 01:01:41 UTC"
#> 
#> $R
#> [1] "R version 4.6.0 (2026-04-24 ucrt)"
#> 
#> $package
#> [1] "1.0.0"
#> 
#> $class
#> NULL
#> 
#> $settings
#> NULL
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
#> $files
#> NULL
#> 
pheno_audit(pheno_project("trial1"))
#> $timestamp
#> [1] "2026-08-25 01:01:41 UTC"
#> 
#> $R
#> [1] "R version 4.6.0 (2026-04-24 ucrt)"
#> 
#> $package
#> [1] "1.0.0"
#> 
#> $class
#> [1] "pheno_project"
#> 
#> $settings
#> NULL
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
#> $files
#> NULL
#> 
f <- tempfile(); writeLines("phenotyping",f); pheno_audit(files=f)$files
#> # A tibble: 1 × 2
#>   path                                                        sha256            
#>   <chr>                                                       <chr>             
#> 1 C:/Users/wep69/AppData/Local/Temp/RtmpGMA6XO/file38fc1bcd72 12851a1f12e7126bc…
```
