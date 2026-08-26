# Posterior category probabilities for an ordinal fit

Part of the bayesAgriFlow 1.0.0 complex-systems workflow. The scientific
data structure is stored separately from the computational backend.

## Usage

``` r
bayes_ordinal_probabilities(fit, newdata = NULL, level = 0.95,
                            re_formula = NA, ndraws = NULL)
```

## Arguments

- fit:

  A brms-backed ordinal fit.

- newdata:

  Optional prediction data.

- level:

  Credible interval mass.

- re_formula:

  Group-level prediction specification.

- ndraws:

  Optional number of draws.

## Value

An auditable design object or posterior summary as described above.

## Details

Version 1.0.0 routes this structure to optional brms/Stan. Priors,
posterior predictive checks, sampler diagnostics, and sensitivity to
structural assumptions should be reported explicitly. Complex models are
not automatically validated by successful computation alone.

## Examples

``` r
# Example 1
if (FALSE) bayes_ordinal_probabilities(ordinal_fit)

# Example 2
if (FALSE) bayes_ordinal_probabilities(ordinal_fit, newdata = ordinal_fit$design$data[1:3, ])

# Example 3
if (FALSE) bayes_ordinal_probabilities(ordinal_fit, level = 0.90)
```
