# Cluster-based inference on a scale-by-time statistic field

Controls field-wise false positives by comparing observed contiguous
supra-threshold regions with the maximum cluster statistic obtained from
each surrogate field.

## Usage

``` r
awi_cluster_test(observed, surrogate_fields, alpha = 0.05,
  threshold = NULL, connectivity = c(8L, 4L),
  statistic = c("mass", "size"), mask = NULL)
```

## Arguments

- observed:

  Numeric scale-by-time statistic matrix.

- surrogate_fields:

  Numeric array with dimensions scale by time by surrogate replicate.

- alpha:

  Cluster-level error rate and, when `threshold = NULL`, pointwise
  threshold quantile.

- threshold:

  `NULL`, one finite scalar, or a matrix matching `observed`. If `NULL`,
  a pointwise \\1-alpha\\ quantile is estimated from the surrogate
  ensemble.

- connectivity:

  Eight- or four-neighbour lattice connectivity.

- statistic:

  Cluster excess mass above threshold or cluster size.

- mask:

  Optional logical matrix restricting the inferential domain.

## Value

An S3 `awi_cluster_test` object containing cluster labels, cluster-level
Monte Carlo p-values, the maximum-cluster null distribution, and the
significant-cell mask.

## Details

A significant cluster supports a field-level departure from the null
within a connected region. It does not provide exact inferential
precision for the onset, offset, or boundary of that cluster. The method
is implemented generically for the scale-by-time lattice and should be
paired with a scientifically defensible surrogate null.

## References

Maris E, Oostenveld R (2007). Nonparametric statistical testing of EEG-
and MEG-data. Journal of Neuroscience Methods 164, 177-190.
doi:10.1016/j.jneumeth.2007.03.024.
