# List registered engines

List registered engines

## Usage

``` r
pheno_engines(capability = NULL)
```

## Arguments

- capability:

  Optional capability regular expression.

## Value

A filtered capability table.

## Examples

``` r
pheno_engines()
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
pheno_engines("texture")
#> # A tibble: 1 × 5
#>   capability engine package available status   
#>   <chr>      <chr>  <chr>   <lgl>     <chr>    
#> 1 texture    native NA      TRUE      available
pheno_engines("deep|class|segment")
#> # A tibble: 5 × 5
#>   capability            engine                 package available status   
#>   <chr>                 <chr>                  <chr>   <lgl>     <chr>    
#> 1 segmentation          native/torch           NA      TRUE      available
#> 2 deep_learning         torch                  torch   TRUE      available
#> 3 neural_segmentation   torch                  torch   TRUE      available
#> 4 image_classification  torch                  torch   TRUE      available
#> 5 instance_segmentation function/yolo/external NA      TRUE      available
```
