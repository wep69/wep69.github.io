# Resolve and normalize criterion weights

Weights are deliberately separate from crop requirements: crop profiles
describe agronomic response, whereas weights describe an aggregation or
decision rule. This separation allows later AHP/TOPSIS modules to supply
weights without mutating crop profiles.

## Usage

``` r
suit_weights(criteria, weights = NULL, normalize = TRUE)
```

## Arguments

- criteria:

  Character vector of unique criterion names, or a supported score
  object from which names can be inferred.

- weights:

  NULL for equal weights, or a non-negative numeric vector. Named
  weights are reordered to match \`criteria\` and must match exactly.

- normalize:

  If TRUE, divide positive weights by their sum.

## Value

A named numeric weight vector.
