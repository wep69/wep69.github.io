# Retrieve model metadata

Retrieve model metadata

## Usage

``` r
pheno_model_info(model_id)
```

## Arguments

- model_id:

  Registered model identifier.

## Value

A `pheno_model_record`.

## Examples

``` r
pheno_model_register("info_example", "classification", "cnn_small", overwrite = TRUE)
pheno_model_info("info_example")
#> <pheno_model_record> info_example 
#>   task: classification 
#>   architecture: cnn_small 
#>   engine: native  
#>   classes: not recorded 
names(pheno_model_info("info_example"))
#>  [1] "model_id"           "task"               "architecture"      
#>  [4] "classes"            "input_size"         "normalization"     
#>  [7] "training_dataset"   "training_date"      "engine"            
#> [10] "engine_version"     "weights_hash"       "seed"              
#> [13] "device"             "loss"               "optimizer"         
#> [16] "augmentation"       "validation_metrics" "license"           
#> [19] "citation"           "provenance"         "weights_path"      
#> [22] "model"             
```
