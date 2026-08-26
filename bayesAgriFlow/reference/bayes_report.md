# Generate a reproducible Markdown analysis report

Generate a reproducible Markdown analysis report

## Usage

``` r
bayes_report(fit, file = NULL, contrast_factor = NULL,
                         rope = NULL, meaningful = NULL)
```

## Arguments

- fit:

  A \`bayes_fit\`.

- file:

  Output \`.md\` path. If \`NULL\`, returns the Markdown lines.

- contrast_factor:

  Optional factor for posterior contrasts.

- rope:

  Optional contrast ROPE.

- meaningful:

  Optional meaningful-effect threshold.

## Value

Invisibly returns Markdown lines; writes them when \`file\` is supplied.

## Examples

``` r
# Example 1: report returned in memory
f <- bayes_fit(bayes_crd(bayes_teaching_data("crd"), "yield", "treatment"),
               draws = 500)
txt <- bayes_report(f, contrast_factor = "treatment")
head(txt)
#> [1] "# bayesAgriFlow analysis report"       
#> [2] ""                                      
#> [3] "## Scientific and computational scope" 
#> [4] ""                                      
#> [5] "- Design: Completely randomized design"
#> [6] "- Response(s): `yield`"                

# Example 2: include a scientifically defined ROPE
txt2 <- bayes_report(f, contrast_factor = "treatment", rope = c(-0.2, 0.2))
head(txt2)
#> [1] "# bayesAgriFlow analysis report"       
#> [2] ""                                      
#> [3] "## Scientific and computational scope" 
#> [4] ""                                      
#> [5] "- Design: Completely randomized design"
#> [6] "- Response(s): `yield`"                

# Example 3: write a temporary Markdown report
p <- tempfile(fileext = ".md")
bayes_report(f, file = p, contrast_factor = "treatment", meaningful = 0.3)
```
