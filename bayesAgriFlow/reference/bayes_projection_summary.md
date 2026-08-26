# Summarize a projection predictive selection path

Summarize the projected variable-ranking path and obtain a heuristic
suggested model size from `projpred`.

## Usage

``` r
bayes_projection_summary(object, stat = "elpd", pct = 0, ...)
```

## Arguments

- object:

  A `bayes_projection` object.

- stat:

  Performance statistic passed to
  [`projpred::suggest_size()`](https://mc-stan.org/projpred/reference/suggest_size.html).

- pct:

  Permitted predictive sacrifice used by the size heuristic.

- ...:

  Additional arguments passed to projpred summary and suggestion
  methods.

## Value

A list containing ranking, suggested size, predictive performance
summary, and an interpretation note.

## Examples

``` r
# Example 1
if (FALSE) bayes_projection_summary(projection)
# Example 2
if (FALSE) bayes_projection_summary(projection, stat="rmse")
# Example 3
if (FALSE) bayes_projection_summary(projection, pct=.05)
```
