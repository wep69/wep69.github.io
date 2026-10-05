# Seasonal mean of a monthly index by agricultural year

Seasonal mean of a monthly index by agricultural year

## Usage

``` r
seasonal_index(
  values,
  dates = NULL,
  year = NULL,
  month = NULL,
  months = c(11, 12, 1:3),
  start_month = 7L
)
```

## Arguments

- values:

  Monthly index values (for example sea-surface temperature or Nino
  3.4).

- dates:

  \`Date\` of each value, or give \`year\` and \`month\`.

- year, month:

  Alternative to \`dates\`.

- months:

  Months averaged (November to March by default).

- start_month:

  First month of the agricultural year.

## Value

Data frame with \`year\` (agricultural year) and \`value\`.

## Examples

``` r
d <- seq(as.Date("1991-07-01"), by = "month", length.out = 36)
seasonal_index(sin(seq_along(d) / 3), dates = d)
#>   year       value
#> 1 1992  0.64523678
#> 2 1993  0.04473022
#> 3 1994 -0.70371203
```
