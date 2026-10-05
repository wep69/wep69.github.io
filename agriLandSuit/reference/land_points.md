# Build land data from a table of points

Station or plot tables can enter the formal raster workflow
(\`crop_criteria()\`, \`land_suitability()\`,
\`scenario_suitability()\`) without building an artificial grid by hand.
Each row becomes one cell of a one-row raster; the real coordinates and
identifiers are kept in \`metadata\$points\` and are restored by
\`land_point_values()\`.

## Usage

``` r
land_points(
  data,
  climate = NULL,
  soil = NULL,
  terrain = NULL,
  water = NULL,
  constraints = NULL,
  units = NULL,
  id = NULL,
  coords = c("lon", "lat"),
  metadata = list(),
  validate = TRUE
)
```

## Arguments

- data:

  Data frame with one row per point.

- climate, soil, terrain, water, constraints:

  Character vectors naming the columns of \`data\` assigned to each
  domain. Names, when given, become layer names (for example \`c(Pseason
  = "rain_mean")\`).

- units:

  Named unit vector using \`domain.layer\` keys, as in \`land_data()\`.

- id:

  Optional column with point identifiers.

- coords:

  Names of the longitude and latitude columns, if present.

- metadata:

  Additional metadata.

- validate:

  Passed to \`land_data()\`.

## Value

An object of class \`agri_land_points\` inheriting from
\`agri_land_data\`.

## Examples

``` r
st <- data.frame(station = c("A", "B"), lon = c(35, 36), lat = c(-15, -20),
                 Pseason = c(900, 500), Tseason = c(25, 27))
lp <- land_points(st, climate = c("Pseason", "Tseason"), id = "station",
                  units = c(climate.Pseason = "mm", climate.Tseason = "degC"))
lp
#> <agri_land_data>
#>  domains : climate 
#>  layers  : 2 
#>  geometry: 1 x 2 cells; resolution 1 x 1 
#>  CRS     : +proj=longlat +datum=WGS84 +no_defs 
```
