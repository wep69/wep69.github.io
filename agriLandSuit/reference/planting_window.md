# Probability of completing a crop cycle by planting date

For each candidate planting day \`D\` (days since the season reference
date) and crop cycle length \`L\`, a season succeeds when the rains have
started by \`D\` and the season ends no earlier than \`D + L - buffer\`,
where \`buffer\` is the number of days the soil water store can sustain
the crop after the last rains. The window is the set of planting days
whose success probability is within \`tolerance\` of the maximum.

## Usage

``` r
planting_window(
  onset,
  cessation,
  cycle,
  days = seq(45, 150, by = 5),
  buffer = 30,
  tolerance = 0.1,
  group = NULL,
  origin = as.Date("2001-09-01")
)
```

## Arguments

- onset, cessation:

  Onset and cessation days since the reference date, one value per
  season (for example \`season_onset()\$onset_day\`).

- cycle:

  Named numeric vector of cycle lengths (days).

- days:

  Candidate planting days.

- buffer:

  Soil water buffer (days).

- tolerance:

  Probability below the maximum still inside the window.

- group:

  Optional grouping vector (for example station), same length as
  \`onset\`.

- origin:

  Reference date used to label days (any year; only day and month are
  shown).

## Value

An \`agri_planting_window\` list with \`success\` (long table of
probabilities) and \`window\` (maximum probability, best day and window
limits per group and crop).

## Examples

``` r
onset <- c(62, 75, 58, 90, 70, 81); cessation <- c(205, 190, 215, 180, 200, 210)
pw <- planting_window(onset, cessation, cycle = c(maize = 120, cowpea = 80))
pw$window
#>   group   crop p_max best_day window_start window_end best_date start_date
#> 1   all  maize     1       90           90         90    30 nov     30 nov
#> 2   all cowpea     1       90           90        130    30 nov     30 nov
#>   end_date
#> 1   30 nov
#> 2   09 jan
head(pw$success)
#>   group  crop day p_success n
#> 1   all maize  45 0.0000000 6
#> 2   all maize  50 0.0000000 6
#> 3   all maize  55 0.0000000 6
#> 4   all maize  60 0.1666667 6
#> 5   all maize  65 0.3333333 6
#> 6   all maize  70 0.5000000 6
```
