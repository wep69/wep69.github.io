# Build a classified composite land-suitability result

Build a classified composite land-suitability result

## Usage

``` r
land_suitability(
  criteria,
  method = c("limiting", "weighted_arithmetic", "weighted_geometric"),
  weights = NULL,
  constraints = NULL,
  na_policy = c("propagate", "available"),
  epsilon = 1e-12,
  breaks = c(0, 0.25, 0.5, 0.75, 1),
  labels = c("N", "S3", "S2", "S1")
)
```

## Arguments

- criteria:

  Criterion-score input accepted by \`suit_aggregate()\`.

- method:

  One of \`limiting\`, \`weighted_arithmetic\`, or
  \`weighted_geometric\`.

- weights:

  Optional criterion weights. Equal weights are used by default.

- constraints:

  Optional \`agri_constraint_effects\`. Effects are applied after
  criterion aggregation, preserving the unconstrained composite.

- na_policy:

  \`propagate\` keeps missingness explicit; \`available\` uses available
  criteria and renormalizes weights where required.

- epsilon:

  Positive numerical floor used only inside logarithms for the geometric
  calculation; exact zero suitability is restored to zero.

- breaks, labels:

  Classification specification passed to \`suit_classify()\`.

## Value

An \`agri_land_suitability\` object inheriting from
\`agri_suitability\`.

## Examples

``` r
r <- terra::rast(ncols = 4, nrows = 3, xmin = 30, xmax = 34, ymin = -20, ymax = -17, crs = "EPSG:4326")
lay <- function(v, n) { x <- r; terra::values(x) <- v; names(x) <- n; x }
land <- land_data(
  climate = lay(seq(400, 1500, length.out = 12), "Pseason"),
  water = lay(rep(c(60, 100, 140), each = 4), "LGP"),
  soil = lay(rep(c(5.0, 6.2, 7.8), 4), "pH"),
  terrain = lay(rep(c(2, 6, 14, 25), 3), "slope"),
  units = c(climate.Pseason = "mm", water.LGP = "day", soil.pH = "pH", terrain.slope = "degree"))
crop <- crop_profile("demo_maize", "Zea mays", common_name = "maize", requirements = list(
  crop_requirement("Pseason", "climate", "mm", "range", c(400, 600, 1200, 1800), source = "illustrative", source_id = "demo-rain"),
  crop_requirement("LGP", "water", "day", "increasing", c(90, 150), source = "illustrative", source_id = "demo-lgp"),
  crop_requirement("pH", "soil", "pH", "range", c(4.5, 5.5, 7.5, 8.5), source = "illustrative", source_id = "demo-ph"),
  crop_requirement("slope", "terrain", "degree", "decreasing", c(5, 20), source = "illustrative", source_id = "demo-slope")))
crit <- crop_criteria(land, crop)
ls <- land_suitability(crit, method = "limiting")
terra::values(suitability_score(ls))
#>       suitability
#>  [1,]   0.0000000
#>  [2,]   0.0000000
#>  [3,]   0.0000000
#>  [4,]   0.0000000
#>  [5,]   0.1666667
#>  [6,]   0.1666667
#>  [7,]   0.1666667
#>  [8,]   0.0000000
#>  [9,]   0.7000000
#> [10,]   0.5000000
#> [11,]   0.4000000
#> [12,]   0.0000000
rules <- constraint_set(
  land_constraint("steep", "terrain.slope", "exclude", "gt", threshold = 20, unit = "degree"),
  land_constraint("acid", "soil.pH", "cap", "lt", threshold = 5.5, cap = 0.5, unit = "pH"))
eff <- constraint_effects(constraint_evaluate(land, rules))
terra::values(suitability_score(land_suitability(crit, constraints = eff)))
#>       suitability
#>  [1,]   0.0000000
#>  [2,]   0.0000000
#>  [3,]   0.0000000
#>  [4,]          NA
#>  [5,]   0.1666667
#>  [6,]   0.1666667
#>  [7,]   0.1666667
#>  [8,]          NA
#>  [9,]   0.7000000
#> [10,]   0.5000000
#> [11,]   0.4000000
#> [12,]          NA
```
