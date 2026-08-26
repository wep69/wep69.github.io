# Specify a Bayesian Gaussian-process response model

Create a brms Gaussian-process model for one or more numeric predictors,
with optional approximate basis and grouping.

## Usage

``` r
bayes_gp(data, response, predictors, by = NULL, cov = c("exp_quad",
         "matern32", "matern52", "exponential", "matern12"), k = NULL,
         iso = TRUE, scale = TRUE, group = NULL,
         family = c("gaussian", "student"))
```

## Arguments

- data:

  A data frame.

- response:

  Response column name.

- predictors:

  Character vector of predictor columns.

- by:

  Optional factor for varying smooths or Gaussian processes.

- cov:

  Gaussian-process covariance kernel.

- k:

  Basis dimension or approximation size.

- iso:

  Whether the Gaussian process is isotropic.

- scale:

  Whether predictors are internally scaled for the Gaussian process.

- group:

  Optional grouping factor.

- family:

  Response family supported by the wrapper.

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
d <- bayes_teaching_data("gp")
bayes_gp(d, "yield", c("nitrogen", "water"))
#> <bayes_design>
#>   Type: Gaussian-process regression model
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 80
#>   fixed: nitrogen, water

# Example 2
bayes_gp(d, "yield", c("nitrogen", "water"), cov = "matern32")
#> <bayes_design>
#>   Type: Gaussian-process regression model
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 80
#>   fixed: nitrogen, water

# Example 3
bayes_plan(bayes_gp(d, "yield", "nitrogen", k = 20))
#> <bayes_plan>
#>   Design: Gaussian-process regression model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```
