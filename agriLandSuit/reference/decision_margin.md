# Compute the decision margin between the two leading crops

Compute the decision margin between the two leading crops

## Usage

``` r
decision_margin(x, na_policy = c("propagate", "available"))
```

## Arguments

- x:

  Multi-crop comparison input.

- na_policy:

  Missing-value policy.

## Value

An \`agri_crop_decision_margin\` object. A zero margin explicitly
denotes a tie for the highest suitability score.
