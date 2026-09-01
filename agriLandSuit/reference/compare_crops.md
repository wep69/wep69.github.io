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
