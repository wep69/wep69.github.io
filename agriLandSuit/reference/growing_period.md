# Length of the growing period (FAO concept, monthly approximation)

A month is humid when \`P + S_start \>= threshold \* PET\`, where
\`S_start\` is the soil water stored at the start of the month. The
growing period of each season is the longest run of consecutive humid
months within \`months\`, expressed in days.

## Usage

``` r
growing_period(
  P,
  PET,
  storage = NULL,
  month,
  season,
  months = c(10:12, 1:6),
  threshold = 0.5,
  years = NULL,
  feb_days = 28.25,
  run_rule = c("days", "months")
)
```

## Arguments

- P, PET:

  Monthly precipitation and PET matrices (units x months).

- storage:

  Storage at the start of each month (same shape), usually
  \`water_balance_monthly()\$storage_start\`. Zero when \`NULL\`.

- month:

  Integer month of each column.

- season:

  Agricultural-year label of each column (see \`agri_year()\`).

- months:

  Months, in calendar order of the season, searched for the run.

- threshold:

  Fraction of PET defining a humid month (FAO uses 0.5).

- years:

  Seasons to return; default all seasons that contain every month in
  \`months\`.

- feb_days:

  Days assigned to February.

- run_rule:

  How the longest humid run is chosen: \`days\` (default) keeps the run
  with most days; \`months\` keeps the run with most months (the first
  one when two runs have the same number of months) and returns its
  days. The two rules differ only when runs of equal month count have
  different lengths in days.

## Value

Matrix of growing-period length (days), units x seasons.

## Examples

``` r
m <- c(7:12, 1:6)
P <- c(0, 0, 0, 20, 90, 180, 200, 170, 120, 40, 5, 0); E <- rep(110, 12)
wb <- water_balance_monthly(P, E, awc = 100)
growing_period(P, E, wb$storage_start, month = m, season = rep(2001, 12))
#>        2001
#> [1,] 181.25
growing_period(P, E, wb$storage_start, month = m, season = rep(2001, 12), run_rule = "months")
#>        2001
#> [1,] 181.25
```
