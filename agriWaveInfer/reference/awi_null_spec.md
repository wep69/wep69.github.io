# Specify a surrogate null model and field-aware inference policy

Creates a formal, validated contract that records the null-generating
process and the time-frequency field correction used by version 0.3.0.

## Usage

``` r
awi_null_spec(
  method = c("phase", "iaaft", "ar", "moving_block", "stationary_block",
    "block_permutation", "circular_shift"),
  n_surrogates = 199L, seed = NA_integer_, ar_order = 1L,
  block_length = 12L, max_iter = 1000L, tolerance = 1e-8,
  alpha = 0.05,
  field_method = c("BH", "BY", "none", "cluster_mass", "cluster_size"),
  cluster_connectivity = 8L, cluster_stat = c("mass", "size")
)
```

## Arguments

- method:

  Surrogate generator. `"phase"` preserves Fourier amplitudes while
  randomizing phases; `"iaaft"` additionally preserves the observed
  marginal distribution; `"ar"` simulates a fitted AR(p) null; block
  methods preserve local temporal dependence; `"circular_shift"`
  preserves the series exactly up to a cyclic displacement.

- n_surrogates:

  Number of surrogate replicates. Must be at least 19. For stable tail
  probabilities, substantially larger values are usually appropriate.

- seed:

  Non-negative integer seed or `NA`. An explicit seed is locally scoped
  and the caller RNG state is restored; `NA` uses and advances the
  current RNG stream.

- ar_order:

  Fixed autoregressive order for the AR null.

- block_length:

  Block length, or expected block length for the stationary bootstrap.

- max_iter:

  Maximum number of IAAFT iterations.

- tolerance:

  IAAFT convergence tolerance based on change in normalized spectral
  error.

- alpha:

  Target inferential error rate.

- field_method:

  How the empirical p-value field is interpreted: unadjusted,
  Benjamini-Hochberg, Benjamini-Yekutieli, cluster mass, or cluster
  size.

- cluster_connectivity:

  Four- or eight-neighbour connectivity on the scale-by-time lattice.

- cluster_stat:

  Cluster statistic retained in the specification.

## Value

An S7 object of class `AwiNullSpec`.

## Details

Surrogate choice is part of the scientific hypothesis. Phase
randomization targets a linear process with the observed spectrum, IAAFT
additionally preserves the empirical amplitude distribution, AR(p)
encodes a parametric serial-dependence null, and block methods resample
local dependence without assuming an AR form. The package does not treat
these nulls as interchangeable.

## References

Schreiber T, Schmitz A (1996). Improved Surrogate Data for Nonlinearity
Tests. Physical Review Letters 77, 635-638.
doi:10.1103/PhysRevLett.77.635.

Politis DN, Romano JP (1994). The Stationary Bootstrap. Journal of the
American Statistical Association 89, 1303-1313.
doi:10.1080/01621459.1994.10476870.
