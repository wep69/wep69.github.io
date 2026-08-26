# Specify Bayesian additive regression trees

Create an optional PyMC/PyMC-BART design with deterministic R-side
predictor encoding.

## Usage

``` r
bayes_bart(data, response, predictors,
           family = c("gaussian", "bernoulli"), trees = 50L,
           alpha = 0.95, beta = 2,
           leaf_response = c("constant", "linear", "mix"))
```

## Arguments

- data:

  A data frame.

- response:

  Response column name.

- predictors:

  Character vector of predictor columns.

- family:

  Response family supported by the wrapper.

- trees:

  Number of BART trees.

- alpha:

  Tree-depth prior parameter.

- beta:

  Tree-depth prior exponent.

- leaf_response:

  PyMC-BART leaf-response type.

## Value

An object or summary described in the function documentation.

## Details

This interface is part of bayesAgriFlow 1.0.0. Optional brms, Stan, and
Python backends are never installed or invoked silently. Scientific
interpretation should be based on posterior uncertainty, model
diagnostics, and the stated estimand rather than on a single index.

## Examples

``` r
# Example 1
d <- bayes_teaching_data("bart")
bayes_bart(d, "yield", c("nitrogen", "water", "soil_p", "cultivar"))
#> <bayes_design>
#>   Type: Bayesian additive regression trees
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 160
#>   fixed: nitrogen, water, soil_p, cultivar

# Example 2
bayes_bart(d, "yield", c("nitrogen", "water", "soil_p"), trees = 100)
#> <bayes_design>
#>   Type: Bayesian additive regression trees
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 160
#>   fixed: nitrogen, water, soil_p

# Example 3
bayes_plan(bayes_bart(d, "yield", c("nitrogen", "water", "soil_p")))
#> <bayes_plan>
#>   Design: Bayesian additive regression trees
#>   Audit: PASS
#>   Engine: pymc
#>   Reason: Bayesian additive regression trees are implemented through the optional PyMC/PyMC-BART backend in version 1.0.0.
#>   Goals: estimation, prediction
```
