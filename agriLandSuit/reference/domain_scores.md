# Access domain composite scores

Stable accessors for \`agri_domain_suitability\` objects, so that
downstream code does not depend on the internal list layout.

## Usage

``` r
domain_scores(x, domain = NULL)

domain_names(x)
```

## Arguments

- x:

  An \`agri_domain_suitability\` object.

- domain:

  Optional domain name(s) to return.

## Value

\`domain_scores()\` returns a multi-layer \`SpatRaster\` (raster input)
or a numeric matrix with one column per domain; \`domain_names()\`
returns a character vector.

## Examples

``` r
st <- data.frame(id = c("A", "B", "C"), lon = c(35, 33, 37), lat = c(-14, -24, -17),
                 Pseason = c(900, 450, 1300), Tseason = c(25, 27, 22), LGP = c(150, 85, 170), slope = c(2, 9, 4))
lp <- land_points(st, climate = c("Pseason", "Tseason"), water = "LGP", terrain = "slope", id = "id",
                  units = c(climate.Pseason = "mm", climate.Tseason = "degC", water.LGP = "day", terrain.slope = "degree"))
p <- crop_profile_subset(crop_profile_library("maize")$maize, keep = c("Pseason", "Tseason", "LGP", "slope"))
ds <- domain_suitability(crop_criteria(lp, p))
domain_names(ds)
#> [1] "climate" "terrain" "water"  
domain_scores(ds)
#> class       : SpatRaster
#> size        : 1, 3, 3  (nrow, ncol, nlyr)
#> resolution  : 1, 1  (x, y)
#> extent      : 0, 3, 0, 1  (xmin, xmax, ymin, ymax)
#> coord. ref. : lon/lat WGS 84 (EPSG:4326)
#> source(s)   : memory
#> names       : climate, terrain, water
#> min values  :    0.25,   0.125,     0
#> max values  :       1,       1,     1
domain_scores(ds, "water")
#> class       : SpatRaster
#> size        : 1, 3, 1  (nrow, ncol, nlyr)
#> resolution  : 1, 1  (x, y)
#> extent      : 0, 3, 0, 1  (xmin, xmax, ymin, ymax)
#> coord. ref. : lon/lat WGS 84 (EPSG:4326)
#> source(s)   : memory
#> name        : water
#> min value   :     0
#> max value   :     1
```
