# Frequentist crosswalk for teaching

Fits a conventional frequentist model to the same teaching structure
when a reasonably direct comparator is available. The frequentist result
is never used to select, calibrate, or validate the Bayesian conclusion.

## Usage

``` r
bayes_compare_frequentist(fit)
```

## Arguments

- fit:

  A \`bayes_fit\`.

## Value

A list containing a conventional model, a summary object when available,
and an explicit statement of the crosswalk's limitations.

## Examples

``` r
# Example 1: CRD ANOVA crosswalk
f <- bayes_fit(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"), draws = 500)
bayes_compare_frequentist(f)$anova
#> Analysis of Variance Table
#> 
#> Response: yield
#>           Df Sum Sq Mean Sq F value    Pr(>F)    
#> treatment  3 4.1604 1.38681  8.2057 0.0004488 ***
#> Residuals 28 4.7321 0.16901                      
#> ---
#> Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1

# Example 2: split-plot error-stratum crosswalk
if (FALSE) bayes_compare_frequentist(split_plot_fit)

# Example 3: mixed-model crosswalk when lme4 is installed
if (FALSE) bayes_compare_frequentist(mixed_fit)
```
