# Suitability calendar (units by seasons)

Suitability calendar (units by seasons)

## Usage

``` r
plot_suitability_calendar(x)
```

## Arguments

- x:

  Output of \`suit_calendar()\`, with columns \`unit\`, \`class\` and
  either \`year\` or \`member\`; optional \`group\` used for facets.

## Value

A \`ggplot\` object.

## Examples

``` r
set.seed(4)
e <- ensemble_from_scores(matrix(runif(20), 2, dimnames = list(c("A", "B"), NULL)),
                          design = data.frame(year = factor(2001:2010)))
if (requireNamespace("ggplot2", quietly = TRUE)) plot_suitability_calendar(suit_calendar(e))
```
