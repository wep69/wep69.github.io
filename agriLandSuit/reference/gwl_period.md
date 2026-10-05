# Crossing periods of global warming levels

Reads the CMIP6 warming-level table of Hauser et al. (2022, doi
10.5281/zenodo.3591806; one row per model, ensemble member, experiment
and warming level with the 20-year window in which the level is reached)
and summarises the central crossing year across models.

## Usage

``` r
gwl_period(
  x,
  warming_level = c(2, 4),
  experiments = c("ssp245", "ssp585"),
  models = NULL
)
```

## Arguments

- x:

  Path to the CSV (comment lines starting with \`#\` are skipped) or a
  data frame.

- warming_level:

  Warming levels (degC above 1850-1900).

- experiments:

  Experiments, for example \`c("ssp245","ssp585")\`.

- models:

  Optional model subset.

## Value

Data frame with experiment, warming level, number of models reaching it,
and median, minimum and maximum central year.

## Examples

``` r
g <- data.frame(model = c("M1", "M2", "M3", "M1"), ensemble = "r1i1p1f1",
                exp = c("ssp245", "ssp245", "ssp245", "ssp585"), warming_level = 2,
                start_year = c(2030, 2036, 2045, 2026), end_year = c(2049, 2055, 2064, 2045))
gwl_period(g, warming_level = 2)
#>   experiment warming_level n_models median  min  max
#> 1     ssp245             2        3   2046 2040 2055
#> 2     ssp585             2        1   2036 2036 2036
```
