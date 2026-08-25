# Inspect the phenotyping model registry

Inspect the phenotyping model registry

## Usage

``` r
pheno_model_registry(task = NULL)
```

## Arguments

- task:

  Optional regular expression filtering task names.

## Value

A tibble with one row per registered model.

## Examples

``` r
pheno_model_registry()
#> # A tibble: 5 × 6
#>   model_id     task              architecture engine engine_version weights_hash
#>   <chr>        <chr>             <chr>        <chr>  <chr>          <chr>       
#> 1 canopy_demo  semantic_segment… external     native NA             NA          
#> 2 disease_demo classification    cnn_small    torch  0.17.0         NA          
#> 3 info_example classification    cnn_small    native NA             NA          
#> 4 leaf_demo    semantic_segment… unet_small   native NA             NA          
#> 5 load_example classification    cnn_small    native NA             NA          
pheno_model_register("registry_example", "classification", "cnn_small")
pheno_model_registry("class")
#> # A tibble: 4 × 6
#>   model_id         task          architecture engine engine_version weights_hash
#>   <chr>            <chr>         <chr>        <chr>  <chr>          <chr>       
#> 1 disease_demo     classificati… cnn_small    torch  0.17.0         NA          
#> 2 info_example     classificati… cnn_small    native NA             NA          
#> 3 load_example     classificati… cnn_small    native NA             NA          
#> 4 registry_example classificati… cnn_small    native NA             NA          
pheno_model_registry("segment|class")
#> # A tibble: 6 × 6
#>   model_id         task          architecture engine engine_version weights_hash
#>   <chr>            <chr>         <chr>        <chr>  <chr>          <chr>       
#> 1 canopy_demo      semantic_seg… external     native NA             NA          
#> 2 disease_demo     classificati… cnn_small    torch  0.17.0         NA          
#> 3 info_example     classificati… cnn_small    native NA             NA          
#> 4 leaf_demo        semantic_seg… unet_small   native NA             NA          
#> 5 load_example     classificati… cnn_small    native NA             NA          
#> 6 registry_example classificati… cnn_small    native NA             NA          
```
