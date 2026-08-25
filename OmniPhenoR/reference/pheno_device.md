# Resolve the compute device

Resolve the compute device

## Usage

``` r
pheno_device(device = c("auto", "cpu", "cuda"))
```

## Arguments

- device:

  `"auto"`, `"cpu"`, or `"cuda"`.

## Value

A character device label.

## Examples

``` r
pheno_device("auto")
#> [1] "cpu"
pheno_device("cpu")
#> [1] "cpu"
if (requireNamespace("torch", quietly = TRUE)) pheno_device("auto")
#> [1] "cpu"
```
