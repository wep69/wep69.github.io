# Depth-weighted mean of layered soil properties

Depth-weighted mean of layered soil properties

## Usage

``` r
soil_depth_weighted(
  x,
  var,
  depths = c("0-5cm", "5-15cm", "15-30cm"),
  thickness = c(5, 10, 15),
  scale = 1
)
```

## Arguments

- x:

  \`SpatRaster\` (or matrix / data frame) holding one layer per depth.

- var:

  Property prefix, for example \`"phh2o"\`; layers named
  \`\<var\>\_\<depth\>\` are selected. Use \`NULL\` to take all layers
  in order.

- depths:

  Depth labels.

- thickness:

  Thickness of each depth interval (any unit).

- scale:

  Divisor applied to the result (SoilGrids stores pH x 10, texture in
  g/kg, SOC in dg/kg, bulk density in cg/cm3).

## Value

Single-layer \`SpatRaster\` or numeric vector.

## Examples

``` r
m <- cbind(phh2o_0_5 = 55, phh2o_5_15 = 60, phh2o_15_30 = 65)
soil_depth_weighted(m, NULL, scale = 10)
#> [1] 6.166667
```
