# Approximate inclusion Bayes factors from the BayesFactor model space

The calculation assumes equal prior probability over the enumerated
models returned by \`BayesFactor\`. It is provided as a transparent
teaching summary, not as a substitute for a pre-registered
scientifically restricted model set.

## Usage

``` r
bayes_inclusion_bf(fit)
```

## Arguments

- fit:

  A compatible Gaussian ANOVA-like \`bayes_fit\`.

## Value

A data frame with prior inclusion odds, posterior inclusion odds, and
inclusion BF.

## Examples

``` r
# Example 1: CRD inclusion evidence
if (requireNamespace("BayesFactor", quietly = TRUE)) {
  f <- bayes_fit(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"),
                 engine = "bayesfactor")
  bayes_inclusion_bf(f)
}
#>        term prior_inclusion_probability posterior_inclusion_probability
#> 1 treatment                         0.5                       0.9857628
#>   prior_inclusion_odds posterior_inclusion_odds inclusion_BF
#> 1                    1                 69.23839     69.23839

# Example 2: RCBD inclusion evidence
if (requireNamespace("BayesFactor", quietly = TRUE)) {
  f2 <- bayes_fit(bayes_rcbd(bayes_teaching_data("rcbd"), "yield",
                  "cultivar", "block"), engine = "bayesfactor")
  bayes_inclusion_bf(f2)
}
#>       term prior_inclusion_probability posterior_inclusion_probability
#> 1 cultivar                         0.5                       0.9246085
#>   prior_inclusion_odds posterior_inclusion_odds inclusion_BF
#> 1                    1                 12.26409     12.26409

# Example 3: factorial main effects and interactions
if (requireNamespace("BayesFactor", quietly = TRUE)) {
  f3 <- bayes_fit(bayes_factorial(bayes_teaching_data("factorial"), "yield",
                  c("cultivar", "fertilizer"), "block"), engine = "bayesfactor")
  bayes_inclusion_bf(f3)
}
#>                  term prior_inclusion_probability
#> 1            cultivar                         0.6
#> 2          fertilizer                         0.6
#> 3 cultivar:fertilizer                         0.2
#>   posterior_inclusion_probability prior_inclusion_odds posterior_inclusion_odds
#> 1                       0.9992225                 1.50              1285.146887
#> 2                       0.9997093                 1.50              3439.466737
#> 3                       0.5413316                 0.25                 1.180225
#>   inclusion_BF
#> 1   856.764591
#> 2  2292.977825
#> 3     4.720898
```
