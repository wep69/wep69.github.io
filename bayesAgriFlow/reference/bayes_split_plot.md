# Specify a Gaussian split-plot design

Specify a Gaussian split-plot design

## Usage

``` r
bayes_split_plot(data, response, block, whole_plot, subplot,
                             whole_plot_id)
```

## Arguments

- data:

  A data frame.

- response:

  Response column name.

- block:

  Block factor.

- whole_plot:

  Whole-plot treatment factor.

- subplot:

  Subplot treatment factor.

- whole_plot_id:

  Globally unique whole-plot experimental-unit identifier.

## Value

A \`bayes_design\`.

## Examples

``` r
# Example 1
bayes_split_plot(bayes_teaching_data("split_plot"), "yield", "block",
                 "irrigation", "cultivar", "whole_plot_id")
#> <bayes_design>
#>   Type: Split-plot design
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 36
#>   block: block
#>   whole_plot: irrigation
#>   subplot: cultivar
#>   whole_plot_id: whole_plot_id
# Example 2
des <- bayes_split_plot(bayes_teaching_data("split_plot"), "yield", "block",
                        "irrigation", "cultivar", "whole_plot_id")
bayes_design_audit(des)
#> <bayes_design_audit>
#>   Design: Split-plot design
#>   Status: PASS
# Example 3
des <- bayes_split_plot(bayes_teaching_data("split_plot"), "yield", "block",
                        "irrigation", "cultivar", "whole_plot_id")
bayes_plan(des)
#> <bayes_plan>
#>   Design: Split-plot design
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```
