# Available water capacity by the Saxton and Rawls (2006) pedotransfer

Water held between 33 kPa and 1500 kPa, from sand, clay and organic
matter (Saxton and Rawls, 2006, equations 1 and 2), scaled to a
root-zone depth.

## Usage

``` r
soil_awc_ptf(
  sand,
  clay,
  om = NULL,
  soc = NULL,
  om_factor = 1.724,
  depth_mm = 1000,
  limits = c(20, 250),
  method = "saxton_rawls"
)
```

## Arguments

- sand, clay:

  Sand and clay content (percent); numeric or \`SpatRaster\`.

- om:

  Organic matter (percent). Alternatively give \`soc\` in g/kg, which is
  converted with \`om = soc / 10 \* om_factor\`.

- soc:

  Soil organic carbon (g/kg).

- om_factor:

  Van Bemmelen conversion factor.

- depth_mm:

  Root-zone depth (mm) over which the volumetric water is integrated.

- limits:

  Optional \`c(min, max)\` bounds of the result (mm).

- method:

  Pedotransfer function; currently \`saxton_rawls\`.

## Value

AWC in mm, same type as the inputs.

## References

Saxton, K.E., Rawls, W.J. (2006). Soil water characteristic estimates by
texture and organic matter for hydrologic solutions. Soil Science
Society of America Journal 70, 1569-1578.

## Examples

``` r
soil_awc_ptf(sand = 40, clay = 25, soc = 8)
#> [1] 132.2656
```
