# Dry spells within the rainy season

Dry spells within the rainy season

## Usage

``` r
dry_spell_index(rain, dates, seasons, wet_day = 1, min_length = 7L)
```

## Arguments

- rain:

  Daily rainfall vector (one unit).

- dates:

  \`Date\` of each day.

- seasons:

  Data frame with \`onset\` and \`cessation\` Dates, for example from
  \`season_onset()\`.

- wet_day:

  Rain (mm) at or above which a day is wet.

- min_length:

  Minimum length (days) counted as a dry spell.

## Value

\`seasons\` with columns \`max_dry_spell\` and \`n_dry_spells\`.

## Examples

``` r
dates <- seq(as.Date("2000-09-01"), as.Date("2002-08-31"), by = "day")
set.seed(1)
wet <- format(dates, "%m") %in% c("11", "12", "01", "02", "03")
rain <- ifelse(wet, rgamma(length(dates), 0.6, 0.08), rgamma(length(dates), 0.1, 0.5))
on <- season_onset(rain, dates)
dry_spell_index(rain, dates, on, min_length = 7)
#>   unit season      onset  cessation onset_day cessation_day length
#> 1    1   2001 2000-11-01 2001-04-27        61           238    177
#> 2    1   2002 2001-10-31 2002-04-27        60           238    178
#>   max_dry_spell n_dry_spells
#> 1            10            1
#> 2            18            2
```
