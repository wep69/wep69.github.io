# Create a deep-learning dataset specification

Stores image-level identity and optional biological grouping before any
train/validation/test split. Images can be arrays or file paths; targets
can be class labels, masks, or file paths. The object itself does not
load data into GPU memory.

## Usage

``` r
pheno_dl_dataset(
  images,
  targets = NULL,
  ids = NULL,
  groups = NULL,
  task = c("semantic_segmentation", "classification"),
  metadata = NULL
)
```

## Arguments

- images:

  List/vector of images or image paths.

- targets:

  Optional targets aligned with `images`.

- ids:

  Optional unique image identifiers.

- groups:

  Optional biological/experimental grouping variable such as plant,
  plot, genotype, environment, or acquisition session.

- task:

  `"semantic_segmentation"` or `"classification"`.

- metadata:

  Optional data frame with one row per image.

## Value

A `pheno_dl_dataset` object.

## Examples

``` r
imgs <- list(pheno_data("leaf_rgb"), pheno_data("leaf_rgb"))
ds1 <- pheno_dl_dataset(imgs, targets = c("healthy", "diseased"), task = "classification")
ds1
#> <pheno_dl_dataset>
#>   task: classification 
#>   images: 2 
#>   groups: 2 
#>   targets: yes 
ds2 <- pheno_dl_dataset(imgs, targets = list(pheno_data("leaf_mask"), pheno_data("leaf_mask")),
  groups = c("plant1", "plant2"), task = "semantic_segmentation")
ds3 <- pheno_dl_dataset(imgs, ids = c("a", "b"), groups = c("plot1", "plot1"),
  metadata = data.frame(treatment = c("control", "stress")))
```
