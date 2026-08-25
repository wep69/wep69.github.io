# Run a transparent built-in phenotyping pipeline

Executes a workflow on an in-memory image. Results remain separate so
users can inspect masks, probabilities, uncertainty, thresholds, and
texture settings. For `leaf_health_dl`, provide a model through `model=`
or an override of the segmentation step.

## Usage

``` r
pheno_pipeline(x, workflow = "leaf_health", overrides = list(), model = NULL)
```

## Arguments

- x:

  RGB image.

- workflow:

  Workflow name or object from
  [`pheno_workflow()`](https://wep69.github.io/OmniPhenoR/reference/pheno_workflow.md).

- overrides:

  Named list replacing step parameters.

- model:

  Optional model supplied to model-ready segmentation presets.

## Value

A `pheno_pipeline` object.

## Examples

``` r
p1 <- pheno_pipeline(pheno_data("leaf_rgb"),"leaf_area"); p1$results$morphology
#> # A tibble: 1 × 13
#>   object_id area_px perimeter_px centroid_x centroid_y bbox_width_px
#>       <int>   <int>        <int>      <dbl>      <dbl>         <dbl>
#> 1         1    2872          264       64.5       48.5            92
#> # ℹ 7 more variables: bbox_height_px <dbl>, equivalent_diameter_px <dbl>,
#> #   circularity <dbl>, aspect_ratio <dbl>, eccentricity <dbl>,
#> #   major_axis_px <dbl>, minor_axis_px <dbl>
p2 <- pheno_pipeline(pheno_data("leaf_rgb"),"leaf_health"); names(p2$results)
#> [1] "rgb"            "mask"           "cover_fraction" "disease"       
#> [5] "texture"       
mock <- function(z) matrix(.8,dim(z)[1],dim(z)[2])
p3 <- pheno_pipeline(pheno_data("leaf_rgb"),"leaf_health_dl",model=mock); names(p3$results)
#> [1] "rgb"            "prediction"     "mask"           "cover_fraction"
#> [5] "texture"        "uncertainty"   
```
