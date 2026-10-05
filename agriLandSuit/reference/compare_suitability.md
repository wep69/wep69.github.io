# Compare two suitability maps or score vectors

Compare two suitability maps or score vectors

## Usage

``` r
compare_suitability(
  x,
  y,
  breaks = c(0, 0.25, 0.5, 0.75, 1),
  labels = c("N", "S3", "S2", "S1"),
  closed = "left",
  area = NULL
)
```

## Arguments

- x, y:

  Numeric vectors or single-layer \`SpatRaster\` objects on the same
  grid (for example two methods, two periods or two models).

- breaks, labels, closed:

  Class definition.

- area:

  Optional unit areas used to weight the transition matrix.

## Value

An \`agri_suit_comparison\` list with \`summary\` (Spearman and Pearson
correlations, mean difference, class agreement and Cohen's kappa) and
\`transitions\` (class-by-class matrix of counts or areas).

## Examples

``` r
cmp <- compare_suitability(c(0.1, 0.4, 0.6, 0.9, 0.55), c(0.2, 0.3, 0.4, 0.8, 0.7))
cmp$summary
#>   n spearman   pearson mean_difference mean_abs_difference class_agreement
#> 1 5      0.9 0.8621048           -0.03                0.13             0.8
#>       kappa share_worse share_better
#> 1 0.7368421         0.2            0
cmp$transitions
#>     to
#> from N S3 S2 S1
#>   N  1  0  0  0
#>   S3 0  1  0  0
#>   S2 0  1  1  0
#>   S1 0  0  0  1
```
