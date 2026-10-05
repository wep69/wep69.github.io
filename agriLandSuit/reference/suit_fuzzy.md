# Evaluate a fuzzy membership function with a selectable engine

Evaluate a fuzzy membership function with a selectable engine

## Usage

``` r
suit_fuzzy(
  x,
  shape = c("triangular", "trapezoid", "gaussian", "increasing", "decreasing",
    "categorical"),
  parameters = list(),
  engine = c("r", "python", "auto")
)
```

## Arguments

- x:

  Numeric/character values or a suitable \`terra::SpatRaster\`.

- shape:

  One of triangular, trapezoid, gaussian, increasing, decreasing, or
  categorical.

- parameters:

  Named list of parameters required by the selected shape.

- engine:

  \`"r"\` (default), \`"python"\`, or \`"auto"\`.

## Value

Membership values with the same spatial/non-spatial form as \`x\`.

## Examples

``` r
suit_fuzzy(c(300, 600, 900, 1500, 2000), "trapezoid",
           list(absolute_min = 400, optimum_min = 600, optimum_max = 1200, absolute_max = 1800))
#> [1] 0.0 1.0 1.0 0.5 0.0
suit_fuzzy(c(10, 20, 30), "increasing", list(unsuitable = 10, suitable = 30))
#> [1] 0.0 0.5 1.0
```
