# Create a suitability ensemble from scenario/model results

Create a suitability ensemble from scenario/model results

## Usage

``` r
ensemble_suitability(x, members = NULL, weights = NULL)
```

## Arguments

- x:

  An \`agri_scenario_suitability\` object or a list of
  \`agri_suitability\` objects.

- members:

  Optional scenario/member IDs. By default, scenario ensembles use all
  non-baseline members when possible.

- weights:

  Optional non-negative member weights. Equal weights are the default.

## Value

An \`agri_uncertainty_ensemble\`.
