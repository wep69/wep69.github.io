# Plot common phenotyping objects

Creates publication-oriented diagnostic plots while preserving the
numeric result object separately. Version 0.1.0 supports RGB images,
masks, one RGB index layer, and Gabor/GLCM texture summaries.

## Usage

``` r
pheno_plot(
  x,
  type = c("image", "mask", "index", "texture"),
  index = "ExG",
  texture_method = "gabor"
)
```

## Arguments

- x:

  Input image or result object.

- type:

  Plot type: `"image"`, `"mask"`, `"index"`, or `"texture"`.

- index:

  RGB index when `type = "index"`.

- texture_method:

  Texture result name when `type = "texture"`.

## Value

A ggplot object.

## Examples

``` r
pheno_plot(pheno_data("leaf_rgb"),"image")

pheno_plot(pheno_segment(pheno_data("leaf_rgb")),"mask")

pheno_plot(pheno_data("leaf_rgb"),"index",index="ExG")
```
