# Specify a predictor measurement-error model

Part of the bayesAgriFlow 1.0.0 complex-systems workflow. The scientific
data structure is stored separately from the computational backend.

## Usage

``` r
bayes_measurement_error(data, response, fixed, measured_predictor,
                        predictor_se, auxiliary_fixed = NULL,
                        group = NULL, family = c("gaussian", "student"))
```

## Arguments

- data:

  A data frame.

- response:

  Main continuous response.

- fixed:

  Main fixed-effects formula.

- measured_predictor:

  Error-prone continuous predictor.

- predictor_se:

  Known positive standard-error column.

- auxiliary_fixed:

  Fixed effects for the latent predictor model.

- group:

  Optional grouping factors.

- family:

  Main Gaussian or Student-t family.

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
d <- bayes_teaching_data("measurement_error")
bayes_measurement_error(d, "yield", ~ soil_n + cultivar, "soil_n", "soil_n_se")
#> <bayes_design>
#>   Type: Measurement-error model
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 80
#>   fixed: soil_n, cultivar
#>   measured_predictor: soil_n
#>   measurement_sd: soil_n_se

# Example 2
bayes_measurement_error(d, "yield", ~ soil_n + cultivar, "soil_n", "soil_n_se", ~ organic_matter)
#> <bayes_design>
#>   Type: Measurement-error model
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 80
#>   fixed: soil_n, cultivar
#>   measured_predictor: soil_n
#>   measurement_sd: soil_n_se

# Example 3
bayes_plan(bayes_measurement_error(d, "yield", ~ soil_n + cultivar, "soil_n", "soil_n_se"))
#> <bayes_plan>
#>   Design: Measurement-error model
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```
