# Summarize distributional posterior coefficients

Part of the bayesAgriFlow 1.0.0 complex-systems workflow. The scientific
data structure is stored separately from the computational backend.

## Usage

``` r
bayes_distributional_effects(fit, dpar = NULL, level = 0.95)
```

## Arguments

- fit:

  A brms-backed distributional fit.

- dpar:

  Optional distributional parameter name.

- level:

  Credible interval mass.

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
if (FALSE) bayes_distributional_effects(distributional_fit)

# Example 2
if (FALSE) bayes_distributional_effects(distributional_fit, "sigma")

# Example 3
if (FALSE) bayes_distributional_effects(distributional_fit, level = 0.90)
```
