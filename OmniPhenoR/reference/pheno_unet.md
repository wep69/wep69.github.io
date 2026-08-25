# Construct a compact U-Net reference model

Builds a small U-Net-like semantic-segmentation network using the R
`torch` package. It is intentionally compact for teaching, testing, and
small phenotyping datasets; it is not claimed to be a universally
optimal architecture.

## Usage

``` r
pheno_unet(
  in_channels = 3L,
  out_channels = 2L,
  base_channels = 16L,
  dropout = 0
)
```

## Arguments

- in_channels:

  Number of input channels.

- out_channels:

  Number of output classes.

- base_channels:

  Width of the first encoder stage.

- dropout:

  Dropout probability after the bottleneck.

## Value

A torch module.

## References

Ronneberger O, Fischer P, Brox T (2015). U-Net: Convolutional Networks
for Biomedical Image Segmentation. *Lecture Notes in Computer Science*
9351: 234-241.
[doi:10.1007/978-3-319-24574-4_28](https://doi.org/10.1007/978-3-319-24574-4_28)
.

## Examples

``` r
if (requireNamespace("torch", quietly = TRUE)) {
  m1 <- pheno_unet(in_channels = 3, out_channels = 2, base_channels = 4)
  m2 <- pheno_unet(in_channels = 1, out_channels = 3, base_channels = 4, dropout = 0.1)
  class(m1)
}
#> [1] "OmniPhenoR_UNetSmall" "nn_module"           
```
