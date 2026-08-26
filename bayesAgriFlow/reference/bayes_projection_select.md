# Run projection predictive variable selection

Use an optional `projpred` reference-model workflow to search for
smaller projected submodels from a brms-backed reference fit.

## Usage

``` r
bayes_projection_select(fit, cross_validate = TRUE,
                        cv_method = c("LOO", "kfold"), K = 10,
                        method = c("forward", "L1"), nterms_max = NULL,
                        validate_search = TRUE, seed = 20260824, ...)
```

## Arguments

- fit:

  A brms-backed reference `bayes_fit`.

- cross_validate:

  Use cross-validated variable selection when TRUE.

- cv_method:

  Cross-validation method, LOO or kfold.

- K:

  Number of folds for k-fold validation.

- method:

  Projection search method.

- nterms_max:

  Optional maximum number of predictor terms.

- validate_search:

  Whether to validate the search path when supported.

- seed:

  Random seed.

- ...:

  Additional arguments passed to
  [`projpred::cv_varsel()`](https://mc-stan.org/projpred/reference/cv_varsel.html)
  or
  [`projpred::varsel()`](https://mc-stan.org/projpred/reference/varsel.html).

## Value

A `bayes_projection` object retaining the selection object, reference
model, ranking, and suggested size.

## Examples

``` r
# Example 1
if (FALSE) bayes_projection_select(reference_fit)
# Example 2
if (FALSE) bayes_projection_select(reference_fit, cv_method="kfold", K=5)
# Example 3
if (FALSE) bayes_projection_select(reference_fit, cross_validate=FALSE, method="L1", nterms_max=8)
```
