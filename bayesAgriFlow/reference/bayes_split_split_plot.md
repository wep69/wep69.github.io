# Specify a Bayesian split-split-plot design

Specify a Bayesian split-split-plot design

## Usage

``` r
bayes_split_split_plot(data, response, block, whole_plot, subplot,
                                   subsubplot, whole_plot_id, subplot_id)
```

## Arguments

- data:

  A data frame.

- response:

  Response column.

- block:

  Block factor.

- whole_plot:

  Whole-plot treatment factor.

- subplot:

  Subplot treatment factor.

- subsubplot:

  Sub-subplot treatment factor.

- whole_plot_id:

  Unique whole-plot experimental-unit identifier.

- subplot_id:

  Unique subplot experimental-unit identifier.

## Value

A \`bayes_design\`.

## Examples

``` r
# Example 1: create the teaching split-split plot
d <- bayes_teaching_data("split_split")
bayes_split_split_plot(d, "yield", "block", "irrigation", "cultivar",
                      "bioinput", "whole_plot_id", "subplot_id")
#> <bayes_design>
#>   Type: Split-split-plot design
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 48
#>   block: block
#>   whole_plot: irrigation
#>   subplot: cultivar
#>   whole_plot_id: whole_plot_id
#>   subsubplot: bioinput
#>   subplot_id: subplot_id

# Example 2: inspect the experimental-unit audit
des <- bayes_split_split_plot(d, "yield", "block", "irrigation", "cultivar",
                             "bioinput", "whole_plot_id", "subplot_id")
bayes_design_audit(des)
#> <bayes_design_audit>
#>   Design: Split-split-plot design
#>   Status: PASS

# Example 3: inspect backend routing
bayes_plan(des)
#> <bayes_plan>
#>   Design: Split-split-plot design
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```
