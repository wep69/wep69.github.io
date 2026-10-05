# Plot a class transition matrix

Plot a class transition matrix

## Usage

``` r
plot_transitions(x)
```

## Arguments

- x:

  An \`agri_suit_comparison\` (from \`compare_suitability()\`) or a
  square matrix with classes in rows (from) and columns (to).

## Value

A \`ggplot\` tile plot with shares of the total.

## Examples

``` r
if (requireNamespace("ggplot2", quietly = TRUE)) plot_transitions(compare_suitability(c(0.1, 0.4, 0.6, 0.9, 0.55), c(0.2, 0.3, 0.4, 0.8, 0.7)))
```
