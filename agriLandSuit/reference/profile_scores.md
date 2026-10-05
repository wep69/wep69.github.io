# Score plain criterion values against a crop profile

Evaluates every requirement of \`profile\` on the matching element of
\`values\`, so that the profile remains the single source of thresholds.
This is the light-weight path for station tables and cell matrices that
do not need an \`agri_land_data\` object.

## Usage

``` r
profile_scores(profile, values, drop = NULL, strict = TRUE)
```

## Arguments

- profile:

  An \`agri_crop_profile\`.

- values:

  Named list or data frame; names must match requirement criteria.

- drop:

  Optional criteria to skip (for example \`"CV"\` for yearly members).

- strict:

  When \`TRUE\`, a missing criterion is an error; otherwise it is
  skipped with a warning.

## Value

Numeric matrix of scores in \[0, 1\], units in rows and criteria in
columns.

## Examples

``` r
p <- crop_profile("demo", "Zea mays", requirements = list(
  crop_requirement("Pseason", "climate", "mm", "range", c(400, 600, 1200, 1800),
                   source = "demo", source_id = "demo")))
profile_scores(p, list(Pseason = c(350, 700, 2000)))
#>      Pseason
#> [1,]       0
#> [2,]       1
#> [3,]       0
```
