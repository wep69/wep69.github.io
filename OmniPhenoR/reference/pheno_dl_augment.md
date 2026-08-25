# Apply auditable image augmentation

Applies selected geometric and photometric transformations while
returning a transformation log. Geometric operations are synchronised
with an optional segmentation target. Strong colour changes can alter
biological colour phenotypes, so `phenotype_preserving = TRUE` rejects
colour augmentation.

## Usage

``` r
pheno_dl_augment(
  x,
  target = NULL,
  operations = c("flip_h", "flip_v", "rotate90"),
  probability = 0.5,
  seed = 123,
  brightness = c(0.9, 1.1),
  contrast = c(0.9, 1.1),
  gamma = c(0.9, 1.1),
  noise_sd = 0.02,
  phenotype_preserving = TRUE
)
```

## Arguments

- x:

  RGB image array or numeric matrix.

- target:

  Optional segmentation target matrix.

- operations:

  Character vector among `flip_h`, `flip_v`, `rotate90`, `brightness`,
  `contrast`, `gamma`, `noise`, and `blur`.

- probability:

  Per-operation application probability.

- seed:

  Random seed.

- brightness:

  Multiplicative brightness range.

- contrast:

  Multiplicative contrast range.

- gamma:

  Gamma range.

- noise_sd:

  Maximum Gaussian noise standard deviation on unit scale.

- phenotype_preserving:

  If `TRUE`, reject photometric augmentation that can change a
  colour-derived phenotype.

## Value

A list with augmented image, target, log, and seed.

## References

Shorten C, Khoshgoftaar TM (2019). A survey on Image Data Augmentation
for Deep Learning. *Journal of Big Data* 6:60.
[doi:10.1186/s40537-019-0197-0](https://doi.org/10.1186/s40537-019-0197-0)
.

## Examples

``` r
a1 <- pheno_dl_augment(pheno_data("leaf_rgb"), pheno_data("leaf_mask"),
  operations = c("flip_h", "rotate90"), probability = 1, seed = 1)
dim(a1$image); dim(a1$target)
#> [1] 128  96   3
#> [1] 128  96
a2 <- pheno_dl_augment(pheno_data("leaf_rgb"), operations = "noise", probability = 1,
  seed = 2, phenotype_preserving = FALSE)
a3 <- pheno_dl_augment(pheno_data("leaf_gray"), operations = "blur", probability = 1,
  seed = 3, phenotype_preserving = FALSE)
```
