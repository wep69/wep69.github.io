# Segment with PlantCV or an explicit PlantCV adapter

The explicit `adapter` route is recommended for stable scientific
workflows because PlantCV workflows are highly configurable. A
conservative built-in path is provided for basic RGB files when PlantCV
is installed, but should be validated for each deployment domain.

## Usage

``` r
pheno_plantcv_segment(
  x,
  adapter = NULL,
  object_type = c("light", "dark"),
  image_id = NULL,
  ...
)
```

## Arguments

- x:

  RGB image or existing image file.

- adapter:

  Optional R function performing PlantCV-compatible segmentation.

- object_type:

  PlantCV Otsu object type for the basic built-in path.

- image_id:

  Optional identifier.

- ...:

  Passed to adapter.

## Value

`pheno_prediction`.

## Examples

``` r
mock<-function(z) pheno_segment(z,"ExG")
p<-pheno_plantcv_segment(pheno_data("leaf_rgb"),adapter=mock); p$engine
#> [1] "plantcv_adapter"
pheno_plantcv_segment(pheno_data("leaf_rgb"),adapter=function(z) pheno_data("leaf_mask"))
#> <pheno_prediction>
#>   engine: plantcv_adapter  device: cpu 
#>   classes: background, foreground 
#>   mask dimensions: 96 x 128 
```
