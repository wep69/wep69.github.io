# Audit an experimental design

Checks missing values, replication, experimental-unit identifiers,
hierarchy, repeated-measures indexing, grouping-factor information,
response-family compatibility, and the specific balance rules used by
the bundled teaching designs. The audit never modifies observations.

## Usage

``` r
bayes_design_audit(design)
```

## Arguments

- design:

  A \`bayes_design\` object.

## Value

A \`bayes_design_audit\` object.

## Examples

``` r
# Example 1: CRD replication audit
bayes_design_audit(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"))
#> <bayes_design_audit>
#>   Design: Completely randomized design
#>   Status: PASS

# Example 2: repeated-measures indexing audit
bayes_design_audit(bayes_repeated(
  bayes_teaching_data("repeated"), "height", "plot_id", "time", "treatment"))
#> <bayes_design_audit>
#>   Design: Repeated-measures mixed model
#>   Status: PASS

# Example 3: split-split-plot hierarchy audit
bayes_design_audit(bayes_split_split_plot(
  bayes_teaching_data("split_split"), "yield", "block", "irrigation",
  "cultivar", "bioinput", "whole_plot_id", "subplot_id"))
#> <bayes_design_audit>
#>   Design: Split-split-plot design
#>   Status: PASS
```
