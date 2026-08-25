# Group-aware train/validation/test splitting

Splits complete biological groups rather than individual pixels or
repeated images. This is designed to reduce leakage when several images
originate from the same plant, plot, genotype, environment, or
acquisition unit.

## Usage

``` r
pheno_dl_split(
  x,
  group = NULL,
  proportions = c(train = 0.7, validation = 0.15, test = 0.15),
  seed = 123,
  stratify = NULL
)
```

## Arguments

- x:

  `pheno_dl_dataset`, data frame, or integer sample count.

- group:

  Optional group vector or column name. For a `pheno_dl_dataset`, its
  stored `groups` are used by default.

- proportions:

  Train, validation, and test proportions. Names are recommended.

- seed:

  Random seed used to shuffle groups.

- stratify:

  Optional group-level stratification vector. A vector aligned with
  observations is reduced to the first value in each group.

## Value

A `pheno_split` list containing row indices and group assignments.

## References

Kapoor S, Narayanan A (2023). Leakage and the reproducibility crisis in
machine-learning-based science. *Patterns* 4(9):100804.
[doi:10.1016/j.patter.2023.100804](https://doi.org/10.1016/j.patter.2023.100804)
.

## Examples

``` r
ds <- pheno_dl_dataset(as.list(1:12), targets = rep(c("A", "B"), 6),
  groups = rep(paste0("plant", 1:6), each = 2), task = "classification")
s1 <- pheno_dl_split(ds, seed = 7); lengths(s1[c("train", "validation", "test")])
#>      train validation       test 
#>          8          2          2 
s2 <- pheno_dl_split(12, group = rep(1:6, each = 2), proportions = c(.5, .25, .25), seed = 9)
s3 <- pheno_dl_split(data.frame(plot = rep(LETTERS[1:6], each = 2)), group = "plot", seed = 11)
```
