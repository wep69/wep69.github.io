# Deep-learning capability summary

Deep-learning capability summary

## Usage

``` r
pheno_model_capabilities()
```

## Value

A tibble describing torch, torchvision, CUDA, and reference model
availability.

## Examples

``` r
pheno_model_capabilities()
#> # A tibble: 6 × 4
#>   capability     engine      available status       
#>   <chr>          <chr>       <lgl>     <chr>        
#> 1 torch          torch       TRUE      available    
#> 2 torchvision    torchvision TRUE      available    
#> 3 cuda           torch       FALSE     not available
#> 4 unet_small     torch       TRUE      available    
#> 5 cnn_small      torch       TRUE      available    
#> 6 function_model native      TRUE      available    
subset(pheno_model_capabilities(), available)
#> # A tibble: 5 × 4
#>   capability     engine      available status   
#>   <chr>          <chr>       <lgl>     <chr>    
#> 1 torch          torch       TRUE      available
#> 2 torchvision    torchvision TRUE      available
#> 3 unet_small     torch       TRUE      available
#> 4 cnn_small      torch       TRUE      available
#> 5 function_model native      TRUE      available
pheno_model_capabilities()[, c("capability", "status")]
#> # A tibble: 6 × 2
#>   capability     status       
#>   <chr>          <chr>        
#> 1 torch          available    
#> 2 torchvision    available    
#> 3 cuda           not available
#> 4 unet_small     available    
#> 5 cnn_small      available    
#> 6 function_model available    
```
