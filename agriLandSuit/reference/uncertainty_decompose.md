# Decompose marginal sources of ensemble uncertainty

Computes marginal eta-squared (between-group sum of squares divided by
total sum of squares) for each declared source. These are descriptive
marginal shares, not Sobol indices. With correlated or non-orthogonal
source factors, shares can overlap and therefore must not be summed as
an additive variance partition.

## Usage

``` r
uncertainty_decompose(x, design, sources = names(design))
```

## Arguments

- x:

  An \`agri_uncertainty_ensemble\`.

- design:

  A data.frame with one row per ensemble member.

- sources:

  Character vector naming source columns in \`design\`; defaults to all
  columns.

## Value

An \`agri_uncertainty_decomposition\` object.
