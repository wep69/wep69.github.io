# Unified model prediction

Unified model prediction

## Usage

``` r
pheno_predict(x, model, task = NULL, ...)
```

## Arguments

- x:

  Image input.

- model:

  Model object, record, fit, id, or function.

- task:

  Optional task override.

- ...:

  Passed to
  [`pheno_segment_dl()`](https://wep69.github.io/OmniPhenoR/reference/pheno_segment_dl.md)
  or
  [`pheno_classify_dl()`](https://wep69.github.io/OmniPhenoR/reference/pheno_classify_dl.md).

## Value

A `pheno_prediction`.

## Examples

``` r
img <- pheno_data("leaf_rgb")
pheno_predict(img, function(z) c(.3, .7), task = "classification")
#> <pheno_prediction>
#>   engine: function  device: cpu 
#>   classes: class1, class2 
#>   runtime: 0 s
pheno_predict(img, function(z) matrix(.8, dim(z)[1], dim(z)[2]), task = "semantic_segmentation")
#> <pheno_prediction>
#>   engine: function  device: cpu 
#>   classes: background, foreground 
#>   mask dimensions: 96 x 128 
#>   runtime: 0 s
rec <- pheno_model_register(
  "predict_demo", "classification", "function",
  model = function(z) c(.6, .4), overwrite = TRUE
)
pheno_predict(img, "predict_demo")
#> <pheno_prediction>
#>   engine: function  device: cpu 
#>   model: predict_demo 
#>   classes: class1, class2 
#>   runtime: 0 s
```
