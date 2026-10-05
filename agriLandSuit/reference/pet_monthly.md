# Monthly reference or potential evapotranspiration

Three methods are available for monthly series: \* \`thornthwaite\`:
Thornthwaite (1948) with day-length and month-length correction; the
annual heat index is computed from each unit's monthly climatology
unless \`heat_index\` is supplied. \* \`hargreaves\`: Hargreaves and
Samani (1985) with FAO-56 extraterrestrial radiation, \`0.0023 \* 0.408
\* Ra \* (Tmean + 17.8) \* sqrt(Tmax - Tmin)\`, with \`Tmean = (Tmax +
Tmin) / 2\`. \* \`penman_monteith\`: FAO-56 reference evapotranspiration
for monthly steps with soil heat flux set to zero. Missing solar
radiation is estimated with the Hargreaves radiation formula (\`krs =
0.16\`), missing vapour pressure from relative humidity or, failing
that, from \`tmin\`.

## Usage

``` r
pet_monthly(
  tmean = NULL,
  tmax = NULL,
  tmin = NULL,
  lat,
  month,
  method = c("thornthwaite", "hargreaves", "penman_monteith"),
  heat_index = NULL,
  rs = NULL,
  rh = NULL,
  ea = NULL,
  u2 = 2,
  elev = 0,
  feb_days = 28.25
)
```

## Arguments

- tmean, tmax, tmin:

  Temperature (degC); vectors (one unit) or matrices with units in rows
  and months in columns.

- lat:

  Latitude of each unit.

- month:

  Integer month of each column.

- method:

  \`thornthwaite\`, \`hargreaves\`, or \`penman_monteith\`.

- heat_index:

  Optional Thornthwaite heat index per unit.

- rs:

  Solar radiation (MJ m-2 day-1), same shape as temperatures.

- rh:

  Mean relative humidity (percent).

- ea:

  Actual vapour pressure (kPa).

- u2:

  Wind speed at 2 m (m s-1); default 2.

- elev:

  Elevation (m) of each unit, used for the psychrometric constant.

- feb_days:

  Days assigned to February (28.25 averages leap years).

## Value

Evapotranspiration in mm per month, with the shape of the input.

## References

Allen, R.G., Pereira, L.S., Raes, D., Smith, M. (1998). Crop
evapotranspiration. FAO Irrigation and Drainage Paper 56.

## Examples

``` r
pet_monthly(tmean = rep(25, 12), lat = -20, month = 1:12)
#>  [1] 126.1614 111.1907 116.8037 107.6412 106.5473 100.8806 105.3253 109.3428
#>  [9] 111.0292 120.2993 120.9245 127.1766
pet_monthly(tmax = rep(31, 12), tmin = rep(19, 12), lat = -20, month = 1:12,
            method = "hargreaves")
#>  [1] 180.5258 157.3301 157.1625 130.7344 114.5171 100.9702 108.5457 125.6285
#>  [9] 142.9494 166.4282 171.7208 181.3888
```
