# Convert longitudinal measurements to a functional trajectory object

Convert longitudinal measurements to a functional trajectory object

## Usage

``` r
pheno_functional(
  data,
  id,
  time,
  value,
  grid = NULL,
  method = c("linear", "nearest")
)
```

## Arguments

- data:

  Long-form data frame or `pheno_series`.

- id:

  Subject identifier.

- time:

  Time column.

- value:

  Value column.

- grid:

  Common numeric grid; defaults to sorted unique observed times.

- method:

  Interpolation method passed conceptually to `approx`; currently
  `"linear"` or `"nearest"`.

## Value

A `pheno_functional` object with subject-by-time matrix.

## References

Ramsay JO, Silverman BW (2005). *Functional Data Analysis*, 2nd ed.
[doi:10.1007/b98888](https://doi.org/10.1007/b98888) .

## Examples

``` r
d <- subset(pheno_data("growth_series"), trait == "leaf_area")
f1 <- pheno_functional(d,"plant_id","day","value"); dim(f1$matrix)
#> [1] 12  5
f2 <- pheno_functional(d,"plant_id","day","value",grid=seq(0,28,by=2))
f3 <- pheno_functional(d,"plant_id","day","value",grid=seq(0,28,by=7),method="nearest")
```
