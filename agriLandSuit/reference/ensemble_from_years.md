# Interannual ensemble from yearly criteria

Each agricultural year becomes one ensemble member scored with
\`profile\`. Time-invariant criteria (soil, terrain) enter through
\`static\`. The rainfall CV is dropped from members by default, because
interannual variability is already represented by the members
themselves; keeping it would count the same variability twice.
Constraints are applied per member: \`exclude\` sets units to NA and
\`cap\` limits scores to \`cap_value\`.

## Usage

``` r
ensemble_from_years(
  criteria,
  profile,
  static = NULL,
  drop = "CV",
  exclude = NULL,
  cap = NULL,
  cap_value = 0.5,
  years = NULL,
  breaks = c(0, 0.25, 0.5, 0.75, 1),
  labels = c("N", "S3", "S2", "S1")
)
```

## Arguments

- criteria:

  An \`agri_climate_criteria\` object or a named list of unit x year
  matrices.

- profile:

  An \`agri_crop_profile\`.

- static:

  Named list of time-invariant criterion vectors.

- drop:

  Criteria removed from yearly members.

- exclude:

  Logical vector of excluded units.

- cap:

  Logical vector of capped units.

- cap_value:

  Cap applied where \`cap\` is TRUE.

- years:

  Optional member labels; default the season labels.

- breaks, labels:

  Class definition stored with the ensemble.

## Value

An \`agri_uncertainty_ensemble\` with \`design = data.frame(year)\`.

## Examples

``` r
dates <- seq(as.Date("1991-01-01"), by = "month", length.out = 48)
m <- as.integer(format(dates, "%m"))
f <- rep(c(1, 0.7, 1.2, 0.9), each = 12)
P <- rbind(A = ifelse(m %in% c(11, 12, 1:3), 180, 10) * f, B = ifelse(m %in% c(12, 1:2), 150, 5) * rev(f))
T <- rbind(A = 24 + 2 * cos((m - 1) / 6 * pi), B = 26 + 3 * cos((m - 1) / 6 * pi))
E <- pet_monthly(tmean = T, lat = c(-15, -24), month = m)
cc <- climate_criteria(P, E, tmean = T, dates = dates)
p <- crop_profile_library("maize", domains = c("climate", "water"))$maize
e <- ensemble_from_years(cc, p, drop = "CV")
e$draws
#>         Y1992 Y1993       Y1994
#> A 1.000000000     1 1.000000000
#> B 0.004166667     0 0.004166667
suit_risk(e, probs = numeric())
#>   P_N P_S2plus        mean          sd
#> A   0        1 1.000000000 0.000000000
#> B   1        0 0.002777778 0.002405626
```
