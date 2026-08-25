# Inspect available phenotyping capabilities

Reports native and optional engines without installing or downloading
anything. Availability means the R package/engine is locally available,
not that a particular external model has been scientifically validated.

## Usage

``` r
pheno_capabilities()
```

## Value

A tibble with capability, engine, package, and availability.

## Examples

``` r
pheno_capabilities()
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
subset(pheno_capabilities(), capability == "deep_learning")
#> # A tibble: 1 × 5
#>   capability    engine package available status   
#>   <chr>         <chr>  <chr>   <lgl>     <chr>    
#> 1 deep_learning torch  torch   TRUE      available
subset(pheno_capabilities(), available)
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
```
