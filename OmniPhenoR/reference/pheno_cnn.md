# Construct a compact CNN classification model

Construct a compact CNN classification model

## Usage

``` r
pheno_cnn(in_channels = 3L, classes = 2L, base_channels = 16L, dropout = 0.2)
```

## Arguments

- in_channels:

  Number of input channels.

- classes:

  Number of output classes.

- base_channels:

  Width of the first convolution stage.

- dropout:

  Dropout probability before the output layer.

## Value

A torch module.

## Examples

``` r
if (requireNamespace("torch", quietly = TRUE)) {
  c1 <- pheno_cnn(in_channels = 3, classes = 2, base_channels = 4)
  c2 <- pheno_cnn(in_channels = 1, classes = 3, base_channels = 4, dropout = .2)
  class(c1)
}
#> [1] "OmniPhenoR_CNNSmall" "nn_module"          
```
