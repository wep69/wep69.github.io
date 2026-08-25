# Link phenotypic traits to spatial experimental units

Joins trait rows to `sf` geometries using an explicit identifier.
Duplicate identifiers are rejected by default because silent
many-to-many joins can destroy plot/plant identity.

## Usage

``` r
pheno_spatialize(traits, geometry, id, allow_duplicates = FALSE)
```

## Arguments

- traits:

  Data frame of phenotypic traits.

- geometry:

  `sf` object.

- id:

  Trait/geometry identifier column.

- allow_duplicates:

  Allow duplicate IDs if scientifically intentional.

## Value

An `sf` object.

## Examples

``` r
if (requireNamespace("sf", quietly = TRUE)) {
  g <- sf::st_as_sf(
    data.frame(id = 1:2, x = c(0, 1), y = c(0, 1)),
    coords = c("x", "y")
  )
  pheno_spatialize(data.frame(id = 1:2, area = c(20, 25)), g, "id")
}
#> Simple feature collection with 2 features and 2 fields
#> Geometry type: POINT
#> Dimension:     XY
#> Bounding box:  xmin: 0 ymin: 0 xmax: 1 ymax: 1
#> CRS:           NA
#>   id area    geometry
#> 1  1   20 POINT (0 0)
#> 2  2   25 POINT (1 1)
if (requireNamespace("sf", quietly = TRUE)) {
  g <- sf::st_as_sf(
    data.frame(plot = c("P1", "P2"), x = 1:2, y = 1:2),
    coords = c("x", "y")
  )
  traits <- data.frame(plot = c("P1", "P2"), ExG = c(.2, .3))
  pheno_spatialize(traits, g, "plot")
}
#> Simple feature collection with 2 features and 2 fields
#> Geometry type: POINT
#> Dimension:     XY
#> Bounding box:  xmin: 1 ymin: 1 xmax: 2 ymax: 2
#> CRS:           NA
#>   plot ExG    geometry
#> 1   P1 0.2 POINT (1 1)
#> 2   P2 0.3 POINT (2 2)
if (requireNamespace("sf", quietly = TRUE)) {
  g <- sf::st_as_sf(
    data.frame(plant = 1:3, x = 1:3, y = 1:3),
    coords = c("x", "y")
  )
  traits <- data.frame(plant = 1:3, severity = c(2, 5, 1))
  pheno_spatialize(traits, g, "plant")
}
#> Simple feature collection with 3 features and 2 fields
#> Geometry type: POINT
#> Dimension:     XY
#> Bounding box:  xmin: 1 ymin: 1 xmax: 3 ymax: 3
#> CRS:           NA
#>   plant severity    geometry
#> 1     1        2 POINT (1 1)
#> 2     2        5 POINT (2 2)
#> 3     3        1 POINT (3 3)
```
