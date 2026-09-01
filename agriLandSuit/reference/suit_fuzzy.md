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
