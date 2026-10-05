# Resolve an agriLandSuit computation engine

Resolve an agriLandSuit computation engine

## Usage

``` r
agri_engine(engine = c("r", "python", "auto"), python_groups = "spatial")
```

## Arguments

- engine:

  One of \`"r"\`, \`"python"\`, or \`"auto"\`.

- python_groups:

  Python module groups required by a computation.

## Value

The resolved engine as a character scalar.

## Examples

``` r
agri_engine("r")
#> [1] "r"
# \donttest{
# "auto" falls back to R when the optional Python modules are absent
agri_engine("auto", python_groups = "fuzzy")
#> [1] "r"
# }
```
