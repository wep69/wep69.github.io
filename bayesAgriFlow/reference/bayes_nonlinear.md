# Specify a Bayesian nonlinear response model

Create a design-aware nonlinear response model for fitting through brms
and Stan.

## Usage

``` r
bayes_nonlinear(data, response, quantitative, model = c("mitscherlich",
                "michaelis_menten", "logistic4", "gompertz"),
                family = c("gaussian", "student"), parameter_priors = NULL)
```

## Arguments

- data:

  A data frame.

- response:

  Response column name.

- quantitative:

  Numeric quantitative predictor column.

- model:

  Named nonlinear template.

- family:

  Response family supported by the wrapper.

- parameter_priors:

  Optional named prior strings for nonlinear parameters.

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
d <- bayes_teaching_data("nonlinear")
bayes_nonlinear(d, "yield", "nitrogen")
#> <bayes_design>
#>   Type: Nonlinear Bayesian response model
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 42
#>   quantitative: nitrogen

# Example 2
bayes_nonlinear(d, "yield", "nitrogen", model = "michaelis_menten")
#> <bayes_design>
#>   Type: Nonlinear Bayesian response model
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 42
#>   quantitative: nitrogen

# Example 3
bayes_plan(bayes_nonlinear(d, "yield", "nitrogen", family = "student"))
#> <bayes_plan>
#>   Design: Nonlinear Bayesian response model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```
