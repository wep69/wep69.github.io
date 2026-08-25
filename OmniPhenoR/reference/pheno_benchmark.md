# Benchmark phenotyping methods

Runs named functions repeatedly on the same input and records elapsed
time, output object size, a coarse process-memory delta, image
dimensions, and device metadata. Memory delta is not a peak-memory
profiler and should be interpreted only as a screening diagnostic.

## Usage

``` r
pheno_benchmark(x, methods, repetitions = 3L, device = "auto", warmup = TRUE)
```

## Arguments

- x:

  Input image/object.

- methods:

  Named list of functions accepting `x`.

- repetitions:

  Number of repetitions per method.

- device:

  Informational device label or `"auto"`.

- warmup:

  Run each method once before timing.

## Value

A tibble with one row per timed repetition.

## Examples

``` r
img <- pheno_data("leaf_rgb")
b1 <- pheno_benchmark(img, list(exg = function(z) pheno_segment(z, "ExG")), repetitions = 1)
b2 <- pheno_benchmark(
  img, list(rgb = function(z) pheno_rgb_indices(z, c("ExG", "GLI"))),
  repetitions = 2
)
b3 <- pheno_benchmark(
  pheno_data("leaf_gray"), list(first = pheno_first_order),
  repetitions = 1, warmup = FALSE
)
```
