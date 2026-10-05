# Plot shares of an uncertainty decomposition

Plot shares of an uncertainty decomposition

## Usage

``` r
plot_uncertainty(x, units = NULL)
```

## Arguments

- x:

  An \`agri_uncertainty_decomposition\` with matrix shares.

- units:

  Optional unit labels (rows).

## Value

A \`ggplot\` dot chart (units by source).

## Examples

``` r
set.seed(8)
e <- ensemble_from_scores(matrix(runif(36), 3), design = ensemble_design(year = 1:6, AWC = c(75, 150)))
if (requireNamespace("ggplot2", quietly = TRUE)) plot_uncertainty(uncertainty_decompose(e), units = c("A", "B", "C"))
```
