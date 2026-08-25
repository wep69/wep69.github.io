# Inspect a LeafMachine2 installation

Inspect a LeafMachine2 installation

## Usage

``` r
pheno_leafmachine_status(path = NULL)
```

## Arguments

- path:

  Optional LeafMachine2 repository directory.

## Value

One-row tibble.

## References

Weaver WN, Smith SA (2023). From leaves to labels: Building modular
machine learning networks for rapid herbarium specimen analysis with
LeafMachine2. *Applications in Plant Sciences* 11:e11548.
[doi:10.1002/aps3.11548](https://doi.org/10.1002/aps3.11548) .

## Examples

``` r
pheno_leafmachine_status()
#> # A tibble: 1 × 4
#>   available path  version note                                                  
#>   <lgl>     <chr> <chr>   <chr>                                                 
#> 1 FALSE     NA    NA      Set path or option OmniPhenoR.leafmachine2 to a LeafM…
pheno_leafmachine_status(tempdir())
#> # A tibble: 1 × 4
#>   available path                                         version note           
#>   <lgl>     <chr>                                        <chr>   <chr>          
#> 1 FALSE     C:/Users/wep69/AppData/Local/Temp/RtmpGMA6XO NA      Set path or op…
pheno_leafmachine_status(Sys.getenv("LEAFMACHINE2_HOME", unset = tempdir()))
#> # A tibble: 1 × 4
#>   available path                                         version note           
#>   <lgl>     <chr>                                        <chr>   <chr>          
#> 1 FALSE     C:/Users/wep69/AppData/Local/Temp/RtmpGMA6XO NA      Set path or op…
```
