# Make a posterior treatment decision from a fitted experiment

Obtain treatment-level posterior population draws from a compatible
fitted experiment, convert them to utility, choose a Bayes action, and
calculate EVPI.

## Usage

``` r
bayes_treatment_decision(fit, factor, value_per_unit = 1, costs = 0)
```

## Arguments

- fit:

  A compatible `bayes_fit` object.

- factor:

  Treatment factor whose population-level posterior means define
  actions.

- value_per_unit:

  Scalar outcome value for linear utility.

- costs:

  Scalar or one cost per treatment action.

## Value

A `bayes_treatment_decision` object containing posterior outcome draws,
utility, decision, and EVPI.

## Examples

``` r
# Example 1
fit <- bayes_fit(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"),
                 draws = 500, seed = 1)
bayes_treatment_decision(fit, "treatment")
#> bayesAgriFlow agronomic treatment decision
#> Factor: treatment
#> Recommended action: BioC
#>   action expected_utility probability_optimal expected_regret
#>     BioC         6.957556               0.924     0.007206233
#>     BioB         6.671039               0.076     0.293723688
#>     BioA         6.254980               0.000     0.709782036
#>  Control         6.057250               0.000     0.907512452
#> EVPI: 0.0072062
# Example 2
bayes_treatment_decision(fit,"treatment",value_per_unit=1000,costs=c(0,120,240,360))
#> bayesAgriFlow agronomic treatment decision
#> Factor: treatment
#> Recommended action: BioC
#>   action expected_utility probability_optimal expected_regret
#>     BioC         6717.556               0.778        24.59483
#>     BioB         6551.039               0.218       191.11228
#>     BioA         6254.980               0.004       487.17063
#>  Control         5697.250               0.000      1044.90104
#> EVPI: 24.595
# Example 3
bayes_treatment_decision(fit,"treatment")$decision$recommended_action
#> [1] "BioC"
```
