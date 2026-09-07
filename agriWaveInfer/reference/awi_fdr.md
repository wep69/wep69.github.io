# Control false discoveries over a time-frequency p-value field

Treats the time-frequency plane as a multiple-testing field rather than
interpreting each coherence cell as an isolated test.

## Usage

``` r
awi_fdr(p, alpha = 0.05, method = c("BH", "BY"), mask = NULL)
```

## Arguments

- p:

  Numeric matrix of pointwise p-values, conventionally scale by time.

- alpha:

  Target false discovery rate.

- method:

  `"BH"` or the more conservative dependency-robust `"BY"` adjustment.

- mask:

  Optional logical matrix defining the inferential domain, for example
  points outside the cone of influence.

## Value

An S3 `awi_fdr` object containing raw and adjusted p-values, the
significance mask, and counts of tested and rejected cells.

## Details

The procedure flattens only finite cells inside `mask`, applies
[`stats::p.adjust()`](https://rdrr.io/r/stats/p.adjust.html), and maps
adjusted values back to the original scale-by-time geometry.

## References

Benjamini Y, Hochberg Y (1995). Controlling the False Discovery Rate: A
Practical and Powerful Approach to Multiple Testing. Journal of the
Royal Statistical Society Series B 57, 289-300.
doi:10.1111/j.2517-6161.1995.tb02031.x.

Benjamini Y, Yekutieli D (2001). The control of the false discovery rate
in multiple testing under dependency. Annals of Statistics 29,
1165-1188. doi:10.1214/aos/1013699998.
