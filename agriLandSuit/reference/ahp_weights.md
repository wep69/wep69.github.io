# Derive AHP criterion weights

Derive AHP criterion weights

## Usage

``` r
ahp_weights(
  x,
  method = c("eigen", "geometric_mean"),
  consistency_threshold = 0.1,
  warn = TRUE,
  engine = c("r", "python", "auto")
)
```

## Arguments

- x:

  Complete reciprocal pairwise-comparison matrix.

- method:

  R weighting method: principal \`eigen\` vector or row
  \`geometric_mean\`. The Python backend delegates complete matrices to
  \`pymcdm.weights.subjective.AHP\` and is available only for
  \`method="eigen"\`.

- consistency_threshold:

  Threshold used to flag inconsistent judgments.

- warn:

  If TRUE, warn when CR exceeds the threshold.

- engine:

  \`r\` (default), \`python\`, or \`auto\`.

## Value

An \`agri_ahp\` object with weights and consistency diagnostics.

## Examples

``` r
cmp <- data.frame(criterion1 = c("climate", "climate", "soil"),
                  criterion2 = c("soil", "terrain", "terrain"), value = c(2, 5, 3))
m <- ahp_matrix(c("climate", "soil", "terrain"), cmp)
w <- ahp_weights(m)
w$weights
#>   climate      soil   terrain 
#> 0.5815521 0.3089956 0.1094523 
ahp_weights(m, method = "geometric_mean")$weights
#>   climate      soil   terrain 
#> 0.5815521 0.3089956 0.1094523 
```
