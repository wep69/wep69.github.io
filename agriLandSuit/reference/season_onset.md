# Onset and cessation of the rainy season from daily rainfall

Two widely used definitions are provided. \* \`agronomic\`: onset is the
first day, after the reference date, when the rainfall accumulated over
\`window\` days reaches \`threshold\` mm and no dry spell of
\`dry_days\` days (rain below \`wet_day\`) follows within
\`check_days\`. Cessation is the first day after \`cessation_after\`
days when a daily soil water bucket (\`cessation_awc\`, losing
\`cessation_evap\` mm per day) empties. \* \`liebmann\`: anomalous
accumulation (Liebmann et al., 2007); onset is the day after the minimum
and cessation the day of the maximum of the cumulative daily rainfall
anomaly within the season window.

## Usage

``` r
season_onset(
  rain,
  dates,
  method = c("agronomic", "liebmann"),
  start_month = 9L,
  start_day = 1L,
  threshold = 25,
  window = 3L,
  dry_days = 10L,
  check_days = 30L,
  wet_day = 1,
  cessation_after = 150L,
  cessation_awc = 100,
  cessation_evap = 4
)
```

## Arguments

- rain:

  Daily rainfall (mm), vector or matrix (units x days).

- dates:

  \`Date\` of each day.

- method:

  \`agronomic\` or \`liebmann\`.

- start_month, start_day:

  Reference date opening each 365-day season window (1 September by
  default). Seasons are labelled by the year in which the window ends.

- threshold, window, dry_days, check_days, wet_day:

  Agronomic onset criteria.

- cessation_after, cessation_awc, cessation_evap:

  Agronomic cessation.

## Value

Data frame with \`unit\`, \`season\`, \`onset\`, \`cessation\` (Dates),
\`onset_day\` and \`cessation_day\` (days since the reference date) and
\`length\` (days).

## Examples

``` r
dates <- seq(as.Date("2000-09-01"), as.Date("2002-08-31"), by = "day")
set.seed(1)
wet <- format(dates, "%m") %in% c("11", "12", "01", "02", "03")
rain <- ifelse(wet, rgamma(length(dates), 0.6, 0.08), rgamma(length(dates), 0.1, 0.5))
season_onset(rain, dates, method = "agronomic")
#>   unit season      onset  cessation onset_day cessation_day length
#> 1    1   2001 2000-11-01 2001-04-27        61           238    177
#> 2    1   2002 2001-10-31 2002-04-27        60           238    178
season_onset(rain, dates, method = "liebmann")
#>   unit season      onset  cessation onset_day cessation_day length
#> 1    1   2001 2000-11-01 2001-03-30        61           210    149
#> 2    1   2002 2001-11-01 2002-03-31        61           211    150
```
