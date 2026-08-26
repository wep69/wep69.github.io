# Specify a one-step joint missing-data model

Part of the bayesAgriFlow 1.0.0 complex-systems workflow. The scientific
data structure is stored separately from the computational backend.

## Usage

``` r
bayes_missing(data, response, fixed, impute, imputation_models = NULL,
              group = NULL, family = c("gaussian", "student"),
              impute_response = FALSE)
```

## Arguments

- data:

  A data frame.

- response:

  Main continuous response.

- fixed:

  Main fixed-effects formula.

- impute:

  Continuous predictors containing missing values.

- imputation_models:

  Named list of predictor submodels.

- group:

  Optional grouping factors.

- family:

  Main Gaussian or Student-t family.

- impute_response:

  Model missing main-response values jointly.

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
d <- bayes_teaching_data("missing")
bayes_missing(d, "yield", ~ soil_n + organic_matter, "soil_n")
#> <bayes_design>
#>   Type: Joint missing-data model
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 90
#>   fixed: soil_n, organic_matter
#>   impute: soil_n

# Example 2
bayes_missing(d, "yield", ~ soil_n + organic_matter, "soil_n", list(soil_n = ~ organic_matter))
#> <bayes_design>
#>   Type: Joint missing-data model
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 90
#>   fixed: soil_n, organic_matter
#>   impute: soil_n

# Example 3
bayes_plan(bayes_missing(d, "yield", ~ soil_n + organic_matter, "soil_n"))
#> <bayes_plan>
#>   Design: Joint missing-data model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```
