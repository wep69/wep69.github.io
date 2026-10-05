# One-at-a-time sensitivity of suitability to crop thresholds

Perturbs each limit of each requirement (or shifts all limits of a
criterion) and reports the change in the climatological limiting score,
the number of units changing class and, when yearly criteria are
supplied, the largest change in the probability of an unsuitable season,
P(N).

## Usage

``` r
threshold_sensitivity(
  profile,
  values,
  factors = c(0.9, 1.1),
  mode = c("multiply", "shift"),
  criteria = NULL,
  yearly = NULL,
  static = NULL,
  drop = "CV",
  exclude = NULL,
  cap = NULL,
  cap_value = 0.5,
  breaks = c(0, 0.25, 0.5, 0.75, 1),
  labels = c("N", "S3", "S2", "S1"),
  closed = "left",
  n_threshold = 0.25,
  area = NULL,
  unit_names = NULL
)
```

## Arguments

- profile:

  An \`agri_crop_profile\`.

- values:

  Named list of climatological criterion values (one element per
  criterion, one value per unit).

- factors:

  Multiplicative factors (\`mode = "multiply"\`) or additive shifts
  (\`mode = "shift"\`).

- mode:

  \`multiply\` perturbs one limit at a time; \`shift\` moves all limits
  of a criterion together.

- criteria:

  Optional subset of criteria to perturb.

- yearly:

  Optional \`agri_climate_criteria\` or named list of unit x year
  matrices for the P(N) diagnostic.

- static:

  Optional named list of time-invariant criteria (soil, terrain) used
  with \`yearly\`.

- drop:

  Criteria removed from yearly members.

- exclude, cap, cap_value:

  Constraint handling: units with \`exclude\` TRUE become NA; units with
  \`cap\` TRUE are capped at \`cap_value\`.

- breaks, labels, closed:

  Class definition passed to \`score_to_class()\`.

- n_threshold:

  Score below which a season is unsuitable.

- area:

  Optional unit areas; adds suitable-area columns (score \>= 0.5).

- unit_names:

  Optional unit labels used in \`units_changed\`.

## Value

An \`agri_threshold_sensitivity\` data frame.

## Examples

``` r
p <- crop_profile_library("sorghum", domains = c("climate", "water"))$sorghum
vals <- list(LGP = c(80, 110, 140), Pseason = c(450, 900, 1200), CV = c(0.35, 0.2, 0.25), Tseason = c(26, 28, 24))
yearly <- list(LGP = cbind(c(70, 100, 130), c(90, 120, 150)), Pseason = cbind(c(400, 800, 1100), c(500, 1000, 1300)),
               Tseason = cbind(c(26, 28, 24), c(26, 28, 24)))
ts <- threshold_sensitivity(p, vals, yearly = yearly, unit_names = c("A", "B", "C"))
ts
#> <agri_threshold_sensitivity> 24 perturbations of 4 criteria 
#>  criterion limit_index factor max_abs_change n_class_changes max_abs_change_PN
#>        LGP           1    0.9      0.1269841               0               0.0
#>        LGP           1    1.1      0.1111111               1               0.5
#>        LGP           2    0.9      0.2222222               0               0.0
#>        LGP           2    1.1      0.1637427               1               0.0
#>    Pseason           1    0.9      0.0000000               0               0.0
#>    Pseason           1    1.1      0.0000000               0               0.0
#>    Pseason           2    0.9      0.0000000               0               0.0
#>    Pseason           2    1.1      0.0000000               0               0.0
#>    Pseason           3    0.9      0.0000000               0               0.0
#>    Pseason           3    1.1      0.0000000               0               0.0
threshold_sensitivity(p, vals, factors = c(-1, 1), mode = "shift", criteria = "Tseason", area = c(10, 10, 5))
#> <agri_threshold_sensitivity> 2 perturbations of 1 criteria 
#>  criterion limit_index factor max_abs_change n_class_changes max_abs_change_PN
#>    Tseason          NA     -1     0.05263158               0                NA
#>    Tseason          NA      1     0.05263158               0                NA
```
