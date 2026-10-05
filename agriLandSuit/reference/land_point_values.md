# Extract point values from raster results

Extract point values from raster results

## Usage

``` r
land_point_values(x, points)
```

## Arguments

- x:

  A \`SpatRaster\`, \`agri_suitability\`, or any object with a \`score\`
  raster, computed from an \`agri_land_points\` object.

- points:

  The \`agri_land_points\` object (or its \`metadata\$points\` table).

## Value

Data frame with point identifiers, coordinates (when available) and one
column per layer.

## Examples

``` r
st <- data.frame(station = c("A", "B"), lon = c(35, 36), lat = c(-15, -20), Pseason = c(900, 450), Tseason = c(25, 27))
lp <- land_points(st, climate = c("Pseason", "Tseason"), id = "station",
                  units = c(climate.Pseason = "mm", climate.Tseason = "degC"))
p <- crop_profile_subset(crop_profile_library("maize")$maize, keep = c("Pseason", "Tseason"))
land_point_values(land_suitability(crop_criteria(lp, p)), lp)
#>   point id  x   y suitability
#> 1     1  A 35 -15        1.00
#> 2     2  B 36 -20        0.25
```
