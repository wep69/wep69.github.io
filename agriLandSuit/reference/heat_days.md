# Count hot days per agricultural year

Count hot days per agricultural year

## Usage

``` r
heat_days(
  tmax,
  dates,
  threshold = 35,
  months = c(11, 12, 1:3),
  start_month = 7L
)
```

## Arguments

- tmax:

  Daily maximum temperature, vector or matrix (units x days).

- dates:

  \`Date\` of each day.

- threshold:

  Temperature (degC) above which a day counts.

- months:

  Months included (warm season by default).

- start_month:

  First month of the agricultural year.

## Value

Matrix of counts, units x agricultural years.

## Examples

``` r
dates <- seq(as.Date("2000-07-01"), as.Date("2002-06-30"), by = "day")
tmax <- 30 + 6 * sin(2 * pi * (as.numeric(format(dates, "%j")) - 280) / 365)
heat_days(tmax, dates, threshold = 35)
#>      2001 2002
#> [1,]   69   68
```
