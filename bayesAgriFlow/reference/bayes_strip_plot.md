# Specify a Bayesian strip-plot design

Specify a Bayesian strip-plot design

## Usage

``` r
bayes_strip_plot(data, response, block, strip_row, strip_column,
                             strip_row_id, strip_column_id)
```

## Arguments

- data:

  A data frame.

- response:

  Response column.

- block:

  Block factor.

- strip_row:

  Treatment factor assigned to row-oriented strips.

- strip_column:

  Treatment factor assigned to column-oriented strips.

- strip_row_id:

  Unique row-strip experimental-unit identifier.

- strip_column_id:

  Unique column-strip experimental-unit identifier.

## Value

A \`bayes_design\`.

## Examples

``` r
# Example 1: create a strip-plot design
d <- bayes_teaching_data("strip_plot")
bayes_strip_plot(d, "yield", "block", "nitrogen", "irrigation",
                 "row_strip_id", "column_strip_id")
#> <bayes_design>
#>   Type: Strip-plot design
#>   Response: yield
#>   Family: Gaussian
#>   Observations: 36
#>   block: block
#>   strip_row: nitrogen
#>   strip_column: irrigation
#>   strip_row_id: row_strip_id
#>   strip_column_id: column_strip_id

# Example 2: audit strip randomization
des <- bayes_strip_plot(d, "yield", "block", "nitrogen", "irrigation",
                        "row_strip_id", "column_strip_id")
bayes_design_audit(des)
#> <bayes_design_audit>
#>   Design: Strip-plot design
#>   Status: PASS

# Example 3: inspect the compiled plan
bayes_plan(des)
#> <bayes_plan>
#>   Design: Strip-plot design
#>   Audit: PASS
#>   Engine: brms
#>   Reason: The requested response family, residual structure, or hierarchical design requires the brms/Stan path in version 1.0.0.
#>   Goals: estimation, prediction
```
