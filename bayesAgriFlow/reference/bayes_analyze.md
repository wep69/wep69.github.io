# Run the integrated Bayesian workflow

Run the integrated Bayesian workflow

## Usage

``` r
bayes_analyze(design, engine = "auto", prior = bayes_prior(),
                          compute = NULL, contrast_factor = NULL,
                          rope = NULL, meaningful = NULL, ...)
```

## Arguments

- design:

  A \`bayes_design\`.

- engine:

  Engine passed to \`bayes_fit\`.

- prior:

  Prior specification.

- compute:

  Computation specification.

- contrast_factor:

  Optional factor for immediate posterior contrasts.

- rope:

  Optional ROPE in scientific contrast units.

- meaningful:

  Optional meaningful-effect threshold.

- ...:

  Passed to \`bayes_fit\`.

## Value

A \`bayes_analysis\`.

## Examples

``` r
# Example 1: CRD end-to-end
a1 <- bayes_analyze(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"),
                    contrast_factor = "treatment", rope = c(-0.2, 0.2), draws = 500)
a1
#> <bayes_analysis>
#>   Design: Completely randomized design
#>   Family: Gaussian
#>   Engine: native
#>   Design audit: PASS
#>   Diagnostic status: PASS
#>   Pairwise contrasts: 6

# Example 2: quantitative model
a2 <- bayes_analyze(bayes_polynomial(bayes_teaching_data("dose"), "yield", "nitrogen", 2),
                    draws = 500)
a2$summary
#>       parameter          mean        median           sd         lower
#> 1   (Intercept)  5.3500527106  5.3404607540 2.447593e-01  4.8356183150
#> 2      nitrogen  0.0586263834  0.0587928012 5.551468e-03  0.0480152106
#> 3 I(nitrogen^2) -0.0001909333 -0.0001908626 2.693012e-05 -0.0002458837
#> 4         sigma  0.6054639172  0.6013542669 7.576753e-02  0.4761584305
#>           upper p_positive p_negative pd
#> 1  5.8571702444          1          0  1
#> 2  0.0700084660          1          0  1
#> 3 -0.0001387905          0          1  1
#> 4  0.7822997536          1          0  1

# Example 3: mixed-model workflow after local brms fitting
if (FALSE) {
  des3 <- bayes_mixed(bayes_teaching_data("mixed"), "yield", ~ nitrogen,
                      group = "block", random_slopes = list(block = "nitrogen"))
  a3 <- bayes_analyze(des3, engine = "brms")
}
```
