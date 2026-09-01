# Compute TOPSIS closeness scores

Compute TOPSIS closeness scores

## Usage

``` r
topsis_score(
  x,
  weights = NULL,
  types = NULL,
  engine = c("r", "python", "auto")
)
```

## Arguments

- x:

  Criterion suitability scores as an \`agri_criterion_set\`, matrix,
  data.frame, or multi-layer \`SpatRaster\`.

- weights:

  Optional criterion weights.

- types:

  Criterion direction: \`1\`/\`benefit\` or \`-1\`/\`cost\`. Suitability
  criteria normally use benefit direction for all criteria.

- engine:

  \`r\` (default), \`python\`, or \`auto\`. Python uses PyMCDM and is
  available for matrix/data.frame inputs; raster TOPSIS always uses
  R/terra.

## Value

An \`agri_topsis\` object.
