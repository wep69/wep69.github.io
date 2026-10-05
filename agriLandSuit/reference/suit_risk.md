# Risk metrics of a suitability ensemble

Probability that a member is unsuitable, \`P_N = P(score \<
n_threshold)\`, probability of at least moderate suitability, \`P_S2plus
= P(score \>= s2_threshold)\`, mean, standard deviation and quantiles.
The thresholds are left-closed (a score of exactly 0.5 counts as S2 or
better). Use \`class_probability()\` for the right-closed class
convention of \`suit_classify()\`.

## Usage

``` r
suit_risk(
  x,
  n_threshold = 0.25,
  s2_threshold = 0.5,
  probs = c(0.05, 0.5, 0.95),
  as_raster = TRUE
)
```

## Arguments

- x:

  An \`agri_uncertainty_ensemble\`.

- n_threshold, s2_threshold:

  Score thresholds.

- probs:

  Quantile probabilities.

- as_raster:

  Return a \`SpatRaster\` when the ensemble stores a raster template and
  cell numbers.

## Value

Matrix (units x metrics) or \`SpatRaster\`.

## Examples

``` r
set.seed(5)
sc <- matrix(runif(30), nrow = 3, dimnames = list(c("A", "B", "C"), NULL))
e <- ensemble_from_scores(sc, design = data.frame(year = factor(1991:2000)))
suit_risk(e)
#>   P_N P_S2plus      mean        sd       p05       p50       p95
#> A 0.4      0.4 0.4024878 0.2816839 0.1104530 0.2843995 0.9659641
#> B 0.3      0.4 0.4493135 0.2700478 0.1046501 0.3875257 0.8421794
#> C 0.1      0.6 0.6723725 0.2926488 0.2257173 0.7010575 0.9565001
class_probability(e)$probability
#>   P_N P_S3 P_S2 P_S1
#> A 0.4  0.2  0.3  0.1
#> B 0.3  0.3  0.2  0.2
#> C 0.1  0.3  0.1  0.5
```
