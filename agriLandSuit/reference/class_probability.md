# Compute probability of each suitability class

Compute probability of each suitability class

## Usage

``` r
class_probability(x, breaks = x$breaks, labels = x$labels)
```

## Arguments

- x:

  An \`agri_uncertainty_ensemble\`.

- breaks, labels:

  Optional class definition. Defaults to the ensemble's stored
  classification.

## Value

An \`agri_class_probability\` object.
