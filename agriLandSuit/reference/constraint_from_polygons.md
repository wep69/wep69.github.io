# Raster constraint layer from polygons

Builds an exclusion layer (1 inside, 0 outside) from polygons such as
protected areas. Filters WDPA-style polygons and rasterises them on a
template: a cell is protected when at least \`cover\` of its area lies
inside the selected polygons. Field names follow the 2026 WDPA schema
(\`SITE_TYPE\`, \`REALM\`, \`STATUS\`, \`DESIG_ENG\`); adjust them for
other sources.

## Usage

``` r
constraint_from_polygons(
  x,
  template,
  designations = NULL,
  site_type = "PA",
  status = "Designated",
  exclude_realm = "Marine",
  desig_field = "DESIG_ENG",
  cover = 0.5,
  name = "protected"
)
```

## Arguments

- x:

  \`SpatVector\` of protected-area polygons, or path to a vector file.

- template:

  \`SpatRaster\` defining the grid.

- designations:

  Optional designations kept (matched in \`desig_field\`); \`NULL\`
  keeps all.

- site_type, status:

  Values kept in the \`SITE_TYPE\` and \`STATUS\` fields (ignored when
  the field is absent).

- exclude_realm:

  Realm values dropped (marine areas by default).

- desig_field:

  Field holding the designation.

- cover:

  Minimum covered fraction of a cell.

- name:

  Layer name of the result.

## Value

Single-layer \`SpatRaster\` with 1 (protected) and 0, with attribute
\`polygons\` holding the selected polygons' attribute table.

## Examples

``` r
tm <- terra::rast(ncols = 10, nrows = 10, xmin = 30, xmax = 31, ymin = -20, ymax = -19, crs = "EPSG:4326")
pa <- terra::vect("POLYGON ((30.1 -19.9, 30.5 -19.9, 30.5 -19.5, 30.1 -19.5, 30.1 -19.9))", crs = "EPSG:4326")
pa$DESIG_ENG <- "National Park"; pa$SITE_TYPE <- "PA"; pa$REALM <- "Terrestrial"; pa$STATUS <- "Designated"
m <- constraint_from_polygons(pa, tm, designations = "National Park")
terra::global(m, "sum")
#>           sum
#> protected  16
```
