# PlantCV-derived morphology through OmniPhenoR canonical masks

PlantCV-derived morphology through OmniPhenoR canonical masks

## Usage

``` r
pheno_plantcv_morphology(x, adapter = NULL, ...)
```

## Arguments

- x:

  Image/path or `pheno_prediction`.

- adapter:

  Optional PlantCV segmentation adapter.

- ...:

  Passed to segmentation/morphology.

## Value

Morphology table.

## Examples

``` r
mock<-function(z) pheno_data("leaf_mask")
pheno_plantcv_morphology(pheno_data("leaf_rgb"),adapter=mock)
#> # A tibble: 1 × 13
#>   object_id area_px perimeter_px centroid_x centroid_y bbox_width_px
#>       <int>   <int>        <int>      <dbl>      <dbl>         <dbl>
#> 1         1    2872          264       64.5       48.5            92
#> # ℹ 7 more variables: bbox_height_px <dbl>, equivalent_diameter_px <dbl>,
#> #   circularity <dbl>, aspect_ratio <dbl>, eccentricity <dbl>,
#> #   major_axis_px <dbl>, minor_axis_px <dbl>
p<-pheno_prediction(pheno_data("leaf_mask")); pheno_plantcv_morphology(p)
#> # A tibble: 1 × 13
#>   object_id area_px perimeter_px centroid_x centroid_y bbox_width_px
#>       <int>   <int>        <int>      <dbl>      <dbl>         <dbl>
#> 1         1    2872          264       64.5       48.5            92
#> # ℹ 7 more variables: bbox_height_px <dbl>, equivalent_diameter_px <dbl>,
#> #   circularity <dbl>, aspect_ratio <dbl>, eccentricity <dbl>,
#> #   major_axis_px <dbl>, minor_axis_px <dbl>
```
