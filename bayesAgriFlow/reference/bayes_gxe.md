# Specify a hierarchical genotype-by-environment model

Part of the bayesAgriFlow 1.0.0 complex-systems workflow. The scientific
data structure is stored separately from the computational backend.

## Usage

``` r
bayes_gxe(data, response, genotype, environment, block = NULL,
          genotype_effect = c("random", "fixed"),
          environment_effect = c("fixed", "random"),
          interaction_effect = c("random", "fixed"),
          family = c("gaussian", "student"))
```

## Arguments

- data:

  A multi-environment data frame.

- response:

  Response column.

- genotype:

  Genotype factor.

- environment:

  Environment factor.

- block:

  Optional block factor nested within environment.

- genotype_effect:

  Fixed or random genotype effect.

- environment_effect:

  Fixed or random environment effect.

- interaction_effect:

  Fixed or random G x E effect.

- family:

  Gaussian or Student-t family.

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
d <- bayes_teaching_data("gxe")
bayes_gxe(d, "yield", "genotype", "environment", "block")
#> <bayes_design>
#>   Type: Hierarchical genotype-by-environment model
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 120
#>   block: block
#>   genotype: genotype
#>   environment: environment

# Example 2
bayes_gxe(d, "yield", "genotype", "environment", "block", genotype_effect = "fixed")
#> <bayes_design>
#>   Type: Hierarchical genotype-by-environment model
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 120
#>   block: block
#>   genotype: genotype
#>   environment: environment

# Example 3
bayes_plan(bayes_gxe(d, "yield", "genotype", "environment", "block"))
#> <bayes_plan>
#>   Design: Hierarchical genotype-by-environment model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```
