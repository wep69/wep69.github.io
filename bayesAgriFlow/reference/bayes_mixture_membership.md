# Posterior mixture component memberships

Part of the bayesAgriFlow 1.0.0 complex-systems workflow. The scientific
data structure is stored separately from the computational backend.

## Usage

``` r
bayes_mixture_membership(fit, summary = TRUE, probs = c(0.025, 0.5, 0.975))
```

## Arguments

- fit:

  A brms-backed mixture fit.

- summary:

  Return summarized rather than draw-level probabilities.

- probs:

  Requested posterior quantiles.

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
if (FALSE) bayes_mixture_membership(mixture_fit)

# Example 2
if (FALSE) bayes_mixture_membership(mixture_fit, summary = FALSE)

# Example 3
if (FALSE) bayes_mixture_membership(mixture_fit, probs = c(0.05, 0.5, 0.95))
```
