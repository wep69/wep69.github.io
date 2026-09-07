# Generate surrogate agroclimatic time series

Generates reproducible null-model time series while preserving different
features of the observed process according to the selected scientific
null.

## Usage

``` r
awi_surrogate(x, null = awi_null_spec())

awi_phase_randomize(x, n = 199L, seed = NA_integer_)

awi_iaaft(x, n = 199L, seed = NA_integer_, max_iter = 1000L,
  tolerance = 1e-8)

awi_block_bootstrap(x, n = 199L, block_length = 12L,
  type = c("stationary", "moving"), seed = NA_integer_)
```

## Arguments

- x:

  An `awi_series`.

- null:

  An `AwiNullSpec`.

- n:

  Number of surrogates.

- seed:

  Non-negative integer seed or `NA`. Explicit seeds are locally scoped;
  `NA` uses and advances the current RNG stream.

- max_iter:

  Maximum IAAFT iterations.

- tolerance:

  IAAFT convergence tolerance.

- block_length:

  Block length or expected block length.

- type:

  Stationary or moving-block bootstrap.

## Value

An S3 object of class `awi_surrogate_set` containing a list of surrogate
`awi_series`, diagnostics, the null specification, and RNG provenance.

## Details

Phase randomization preserves the Fourier amplitude spectrum to
floating-point tolerance. IAAFT preserves the observed value
distribution exactly through rank remapping while iteratively
approximating the target spectrum. Stationary bootstrap uses
geometrically distributed block lengths with the specified expected
length. Surrogates should be chosen to represent the null hypothesis,
not selected after inspecting significance results.

## References

Schreiber T, Schmitz A (1996). Improved Surrogate Data for Nonlinearity
Tests. Physical Review Letters 77, 635-638.
doi:10.1103/PhysRevLett.77.635.

Schreiber T, Schmitz A (2000). Surrogate time series. Physica D 142,
346-382. doi:10.1016/S0167-2789(00)00043-9.

Politis DN, Romano JP (1994). The Stationary Bootstrap. Journal of the
American Statistical Association 89, 1303-1313.
doi:10.1080/01621459.1994.10476870.
