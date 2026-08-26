# Predict from a projection-selected submodel

Project the reference posterior to a chosen submodel size and generate
projected predictions for new predictor values.

## Usage

``` r
bayes_projection_predict(object, newdata, nterms = NULL, ndraws = 400,
                         seed = 20260824, ...)
```

## Arguments

- object:

  A `bayes_projection` object.

- newdata:

  New predictor data accepted by projpred.

- nterms:

  Number of selected terms. Defaults to the stored suggested size.

- ndraws:

  Number of projected posterior draws.

- seed:

  Random seed.

- ...:

  Additional arguments for projection or prediction.

## Value

A list containing the projection object, prediction draws, selected
size, new data, and seed.

## Examples

``` r
# Example 1
if (FALSE) bayes_projection_predict(projection, newdata=new_x)
# Example 2
if (FALSE) bayes_projection_predict(projection, newdata=new_x, nterms=5)
# Example 3
if (FALSE) bayes_projection_predict(projection, newdata=new_x, ndraws=1000)
```
