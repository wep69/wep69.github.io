# Specify a Bayesian smooth response model

Create a brms/mgcv smooth-response design using a spline term and
optional grouping structure.

## Usage

``` r
bayes_smooth(data, response, quantitative, by = NULL, k = 10L,
             basis = "tp", group = NULL, family = c("gaussian", "student"))
```

## Arguments

- data:

  A data frame.

- response:

  Response column name.

- quantitative:

  Numeric quantitative predictor column.

- by:

  Optional factor for varying smooths or Gaussian processes.

- k:

  Basis dimension or approximation size.

- basis:

  Spline basis supplied to the smooth term.

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
d <- bayes_teaching_data("smooth")
bayes_smooth(d, "biomass", "soil_moisture")
#> <bayes_design>
#>   Type: Bayesian smoothing-spline model
#>   Response: biomass
#>   Family: Gaussian
#>   Observations: 54
#>   quantitative: soil_moisture

# Example 2
bayes_smooth(d, "biomass", "soil_moisture", k = 12)
#> <bayes_design>
#>   Type: Bayesian smoothing-spline model
#>   Response: biomass
#>   Family: Gaussian
#>   Observations: 54
#>   quantitative: soil_moisture

# Example 3
bayes_plan(bayes_smooth(d, "biomass", "soil_moisture", family = "student"))
#> <bayes_plan>
#>   Design: Bayesian smoothing-spline model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```
