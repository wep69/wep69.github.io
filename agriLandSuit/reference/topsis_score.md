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

## Examples

``` r
z <- matrix(c(.9, .6, .7, .7, .8, .6, .5, .9, .8, .8, .4, .9), ncol = 3, byrow = TRUE,
            dimnames = list(paste0("zone", 1:4), c("climate", "soil", "terrain")))
t <- topsis_score(z, weights = c(climate = 0.4, soil = 0.35, terrain = 0.25))
t$preference
#>     zone1     zone2     zone3     zone4 
#> 0.5951949 0.5944708 0.5411189 0.4267614 
```
