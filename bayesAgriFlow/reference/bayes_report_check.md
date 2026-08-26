# Audit report completeness against core Bayesian reporting items

Audit report completeness against core Bayesian reporting items

## Usage

``` r
bayes_report_check(fit, rope_supplied = FALSE,
                               sensitivity_done = FALSE, ppc_done = FALSE)
```

## Arguments

- fit:

  A \`bayes_fit\`.

- rope_supplied:

  Whether a justified ROPE was supplied for a decision.

- sensitivity_done:

  Whether a relevant prior sensitivity analysis was completed.

- ppc_done:

  Whether a posterior predictive check was reviewed.

## Value

A data frame of reporting items.

## Examples

``` r
# Example 1: initial report audit
f <- bayes_fit(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"),
               draws = 500)
bayes_report_check(f)
#>                                       item status
#> 1           Design and variables specified   TRUE
#> 2        Likelihood/model formula recorded   TRUE
#> 3                           Prior recorded   TRUE
#> 4             Computation/backend recorded   TRUE
#> 5             Posterior interval available   TRUE
#> 6   Sampler/numerical diagnostics reviewed   TRUE
#> 7      Posterior predictive check reviewed  FALSE
#> 8               Prior sensitivity reviewed  FALSE
#> 9  ROPE justified when used for a decision  FALSE
#> 10                           Seed recorded   TRUE

# Example 2: after PPC and sensitivity work
bayes_report_check(f, sensitivity_done = TRUE, ppc_done = TRUE)
#>                                       item status
#> 1           Design and variables specified   TRUE
#> 2        Likelihood/model formula recorded   TRUE
#> 3                           Prior recorded   TRUE
#> 4             Computation/backend recorded   TRUE
#> 5             Posterior interval available   TRUE
#> 6   Sampler/numerical diagnostics reviewed   TRUE
#> 7      Posterior predictive check reviewed   TRUE
#> 8               Prior sensitivity reviewed   TRUE
#> 9  ROPE justified when used for a decision  FALSE
#> 10                           Seed recorded   TRUE

# Example 3: decision report with a justified ROPE
bayes_report_check(f, rope_supplied = TRUE, sensitivity_done = TRUE,
                   ppc_done = TRUE)
#>                                       item status
#> 1           Design and variables specified   TRUE
#> 2        Likelihood/model formula recorded   TRUE
#> 3                           Prior recorded   TRUE
#> 4             Computation/backend recorded   TRUE
#> 5             Posterior interval available   TRUE
#> 6   Sampler/numerical diagnostics reviewed   TRUE
#> 7      Posterior predictive check reviewed   TRUE
#> 8               Prior sensitivity reviewed   TRUE
#> 9  ROPE justified when used for a decision   TRUE
#> 10                           Seed recorded   TRUE
```
