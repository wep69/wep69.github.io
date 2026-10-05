# Stacked class probabilities per unit

Stacked class probabilities per unit

## Usage

``` r
plot_class_probability(x, labels = c("N", "S3", "S2", "S1"))
```

## Arguments

- x:

  Data frame with a \`unit\` column and class probability columns
  \`P_N\`, \`P_S3\`, \`P_S2\`, \`P_S1\` (as produced by
  \`class_probability()\` and bound to unit names), optionally a
  \`group\` column (crop) used for facets.

- labels:

  Class labels.

## Value

A \`ggplot\` object.

## Examples

``` r
x <- data.frame(unit = c("A", "B", "C"), P_N = c(0.1, 0.5, 0), P_S3 = c(0.2, 0.2, 0.1),
                P_S2 = c(0.3, 0.2, 0.2), P_S1 = c(0.4, 0.1, 0.7))
if (requireNamespace("ggplot2", quietly = TRUE)) plot_class_probability(x)
```
