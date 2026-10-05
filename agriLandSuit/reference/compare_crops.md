# Compare suitability among multiple crops

Builds one aligned multi-crop score object without changing the
individual crop suitability calculations. Crop scores must already have
been computed under scientifically comparable assumptions (scenario,
management, aggregation rule, constraints, and geometry).

## Usage

``` r
compare_crops(x)
```

## Arguments

- x:

  Named list of at least two \`agri_suitability\` objects.

## Value

An \`agri_multi_crop_comparison\` object.

## Examples

``` r
z1 <- cbind(rain = c(.8, .6, .7, .4), temp = c(.9, .8, .9, .6))
z2 <- cbind(rain = c(.7, .65, .7, .5), temp = c(.8, .9, .9, .7))
z3 <- cbind(rain = c(.6, .55, .75, .5), temp = c(1, .9, .8, .8))
cmp <- compare_crops(list(maize = suit_aggregate(z1), bean = suit_aggregate(z2), sorghum = suit_aggregate(z3)))
cmp
#> <agri_multi_crop_comparison> 3 crops: maize, bean, sorghum 
```
