# Tercile groups and ENSO phases

\`tercile_groups()\` splits a numeric index at its 1/3 and 2/3
quantiles. \`enso_phase()\` labels El Nino (index \>= threshold), La
Nina (index \<= -threshold) and neutral seasons.

## Usage

``` r
tercile_groups(x, labels = c("cold", "neutral", "warm"))

enso_phase(x, threshold = 0.5)
```

## Arguments

- x:

  Numeric index, one value per season.

- labels:

  Labels of the lower, middle and upper terciles.

- threshold:

  ENSO threshold (degC) on the Nino 3.4 anomaly.

## Value

Factor.

## Examples

``` r
set.seed(2)
tercile_groups(rnorm(12))
#>  [1] cold    neutral warm    cold    neutral neutral warm    cold    warm   
#> [10] cold    neutral warm   
#> Levels: cold neutral warm
enso_phase(c(1.2, 0.1, -0.8))
#> [1] El Nino neutral La Nina
#> Levels: El Nino neutral La Nina
```
