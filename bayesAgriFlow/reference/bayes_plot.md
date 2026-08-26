# Plot a posterior summary or treatment contrasts

Uses base R graphics so plotting remains available without adding a
graphics package to the core dependency set.

## Usage

``` r
bayes_plot(fit, type = c("effects", "contrasts"), factor = NULL,
                       rope = NULL, ...)
```

## Arguments

- fit:

  A \`bayes_fit\`.

- type:

  \`"effects"\` or \`"contrasts"\`.

- factor:

  Required for contrast plots.

- rope:

  Optional ROPE for contrasts.

- ...:

  Additional graphical arguments.

## Value

The plotted data, invisibly.

## Examples

``` r
# Example 1: coefficient interval plot
f <- bayes_fit(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"),
               draws = 500)
if (interactive()) bayes_plot(f, "effects")

# Example 2: treatment contrast plot
if (interactive()) bayes_plot(f, "contrasts", factor = "treatment")

# Example 3: contrast plot with ROPE reference limits
if (interactive()) bayes_plot(f, "contrasts", factor = "treatment",
                              rope = c(-0.2, 0.2))
```
