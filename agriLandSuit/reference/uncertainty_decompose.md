# Decompose sources of ensemble uncertainty

\`method = "marginal"\` computes marginal eta-squared (between-group sum
of squares divided by total sum of squares) for each declared source.
These are descriptive marginal shares, not Sobol indices; with
non-orthogonal factors they can overlap and must not be summed. \`method
= "anova"\` fits an additive main-effects linear model with the sources
entered in the order given and returns sequential (type I) sums of
squares as shares of the total, plus a \`residual\` share; these shares
do sum to one.

## Usage

``` r
uncertainty_decompose(
  x,
  design = NULL,
  sources = NULL,
  method = c("marginal", "anova")
)
```

## Arguments

- x:

  An \`agri_uncertainty_ensemble\`.

- design:

  A data.frame with one row per ensemble member. When \`NULL\`, the
  design attached to the ensemble (see \`ensemble_set_design()\`,
  \`ensemble_from_years()\`, \`scenario_ensemble()\`) is used.

- sources:

  Character vector naming source columns in \`design\`; defaults to all
  columns.

- method:

  \`marginal\` (default, 1.0.0 behaviour) or \`anova\`.

## Value

An \`agri_uncertainty_decomposition\` object.

## Examples

``` r
set.seed(10)
des <- ensemble_design(year = 1:8, GCM = c("g1", "g2", "g3"))
sc <- matrix(runif(2 * 24), nrow = 2)
e <- ensemble_set_design(ensemble_from_scores(sc), des)
uncertainty_decompose(e)$shares
#>           year        GCM
#> [1,] 0.1808945 0.15668917
#> [2,] 0.2948061 0.08858288
uncertainty_decompose(e, method = "anova")$shares
#>           year        GCM  residual
#> [1,] 0.1808945 0.15668917 0.6624163
#> [2,] 0.2948061 0.08858288 0.6166111
```
