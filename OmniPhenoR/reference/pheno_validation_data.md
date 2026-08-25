# Frozen synthetic validation scenarios for OmniPhenoR 0.2.0

Generates deterministic ground-truth geometry, disease, texture, and
model-probability scenarios. These simulations validate software
behaviour; they are not substitutes for independent field validation.

## Usage

``` r
pheno_validation_data(domain = c("geometry", "disease", "texture", "ml"))
```

## Arguments

- domain:

  `"geometry"`, `"disease"`, `"texture"`, or `"ml"`.

## Value

A named list of deterministic matrices/arrays and truth metadata.

## Examples

``` r
names(pheno_validation_data("geometry"))
#> [1] "circle"             "ellipse"            "rectangle"         
#> [4] "irregular_leaf"     "overlapping_leaves"
pheno_validation_data("disease")$truth_percent
#> [1]  5 10 25 50 75
dim(pheno_validation_data("ml")$probability)
#> [1]  96 128   2
```
