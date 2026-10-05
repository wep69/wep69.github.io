# Yearly agroclimatic criteria from monthly series

Computes, for every unit (station or raster cell) and agricultural year,
the criteria used by rainfed suitability profiles: length of growing
period (\`LGP\`, days, FAO concept with a monthly Thornthwaite-Mather
bucket), seasonal rainfall (\`Pseason\`), annual rainfall over the
agricultural year (\`Pannual\`), mean temperature of the warm season
(\`Tseason\`), mean temperature of the warmest month (\`Twarm\`),
maximum temperature of the warmest month (\`Txwarm\`, when \`tmax\` is
given) and seasonal PET (\`PETseason\`).

## Usage

``` r
climate_criteria(
  P,
  PET,
  tmean = NULL,
  tmax = NULL,
  tmin = NULL,
  dates = NULL,
  year = NULL,
  month = NULL,
  awc = 100,
  awc_na = 100,
  s0 = NULL,
  start_month = 7L,
  lgp_months = c(10:12, 1:6),
  rain_months = c(11, 12, 1:4),
  temp_months = c(11, 12, 1:3),
  threshold = 0.5,
  years = NULL,
  feb_days = 28.25,
  run_rule = c("days", "months")
)
```

## Arguments

- P, PET:

  Monthly precipitation and PET (mm). Matrices with units in rows and
  months in columns, numeric vectors for one unit, or multi-layer
  \`SpatRaster\` objects with one layer per month.

- tmean, tmax, tmin:

  Monthly temperatures (degC), same layout. \`tmean\` defaults to
  \`(tmax + tmin) / 2\`.

- dates:

  \`Date\` of each month (column or layer). Alternatively give \`year\`
  and \`month\`.

- year, month:

  Calendar year and month of each column.

- awc:

  Available water capacity (mm): one value, one per unit, or a
  single-layer \`SpatRaster\` for raster input.

- awc_na:

  Value used where a raster \`awc\` is missing.

- s0:

  Initial soil water storage; default \`awc / 2\`.

- start_month:

  First month of the agricultural year (July by default).

- lgp_months:

  Months searched for the growing period, in season order.

- rain_months:

  Months summed into \`Pseason\`.

- temp_months:

  Months used for \`Tseason\`, \`Twarm\` and \`Txwarm\`.

- threshold:

  Humid-month threshold as a fraction of PET.

- years:

  Optional seasons to keep.

- feb_days:

  Days assigned to February.

- run_rule:

  Rule selecting the longest humid run, see \`growing_period()\`.

## Value

An \`agri_climate_criteria\` object: a list with \`criteria\` (named
list of unit x year matrices), \`years\`, \`units\`, \`awc\`,
\`settings\` and, for raster input, \`template\` and \`cells\`.

## Details

The water balance runs continuously over the whole series, so months
before the first complete season only spin up the storage. Only seasons
that contain all twelve months are returned unless \`years\` is given.

## Examples

``` r
dates <- seq(as.Date("1991-01-01"), by = "month", length.out = 36)
m <- as.integer(format(dates, "%m"))
P <- ifelse(m %in% c(11, 12, 1, 2, 3), 180, 10)
T <- 24 + 3 * cos((m - 1) / 12 * 2 * pi)
E <- pet_monthly(tmean = T, lat = -20, month = m)
cc <- climate_criteria(P, E, tmean = T, dates = dates)
cc$criteria$LGP
#>     1992   1993
#> 1 181.25 181.25
```
