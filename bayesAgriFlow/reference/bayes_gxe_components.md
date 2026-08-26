# Extract genotype-by-environment variance components

Part of the bayesAgriFlow 1.0.0 complex-systems workflow. The scientific
data structure is stored separately from the computational backend.

## Usage

``` r
bayes_gxe_components(fit, level = 0.95)
```

## Arguments

- fit:

  A G x E fit.

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
if (FALSE) bayes_gxe_components(gxe_fit)

# Example 2
if (FALSE) bayes_gxe_components(gxe_fit, 0.90)

# Example 3
d <- bayes_teaching_data("gxe")
bayes_plan(bayes_gxe(d, "yield", "genotype", "environment", "block"))
#> <bayes_plan>
#>   Design: Hierarchical genotype-by-environment model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```
