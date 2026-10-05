# Aggregate criteria within each environmental domain

Aggregate criteria within each environmental domain

## Usage

``` r
domain_suitability(
  x,
  method = c("limiting", "weighted_arithmetic", "weighted_geometric"),
  weights = NULL,
  na_policy = c("propagate", "available"),
  epsilon = 1e-12
)
```

## Arguments

- x:

  An \`agri_criterion_set\` containing crop requirements from one or
  more domains.

- method:

  One of \`limiting\`, \`weighted_arithmetic\`, or
  \`weighted_geometric\`.

- weights:

  Optional criterion weights. Equal weights are used by default.

- na_policy:

  \`propagate\` keeps missingness explicit; \`available\` uses available
  criteria and renormalizes weights where required.

- epsilon:

  Positive numerical floor used only inside logarithms for the geometric
  calculation; exact zero suitability is restored to zero.

## Value

An \`agri_domain_suitability\` object containing one composite per
represented domain. Criterion weights are subset and renormalized within
each domain.

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
ds <- domain_suitability(crit, method = "limiting")
domain_names(ds)
#> [1] "climate" "soil"    "terrain" "water"  
terra::values(domain_scores(ds))
#>         climate soil   terrain     water
#>  [1,] 0.0000000  0.5 1.0000000 0.0000000
#>  [2,] 0.5000000  1.0 0.9333333 0.0000000
#>  [3,] 1.0000000  0.7 0.4000000 0.0000000
#>  [4,] 1.0000000  0.5 0.0000000 0.0000000
#>  [5,] 1.0000000  1.0 1.0000000 0.1666667
#>  [6,] 1.0000000  0.7 0.9333333 0.1666667
#>  [7,] 1.0000000  0.5 0.4000000 0.1666667
#>  [8,] 1.0000000  1.0 0.0000000 0.1666667
#>  [9,] 1.0000000  0.7 1.0000000 0.8333333
#> [10,] 0.8333333  0.5 0.9333333 0.8333333
#> [11,] 0.6666667  1.0 0.4000000 0.8333333
#> [12,] 0.5000000  0.7 0.0000000 0.8333333
```
