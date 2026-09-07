# Surrogate Null Models for Agroclimatic Wavelet Inference

## Why the null model matters

Wavelet significance depends on the serial structure that is considered
compatible with the null hypothesis. The traditional red-noise framework
is important (Torrence and Compo 1998), but an AR(1) process is not a
universal representation of agroclimatic time series. Version 0.3.0
therefore makes the null-generating mechanism an explicit scientific
object.

``` r

library(agriWaveInfer)

null <- awi_null_spec(
  method = "phase",
  n_surrogates = 499,
  seed = 20260904,
  alpha = 0.05,
  field_method = "BH"
)
null
#> <agriWaveInfer::AwiNullSpec>
#>  @ method              : chr "phase"
#>  @ n_surrogates        : num 499
#>  @ seed                : num 20260904
#>  @ ar_order            : num 1
#>  @ block_length        : num 12
#>  @ max_iter            : num 1000
#>  @ tolerance           : num 1e-08
#>  @ alpha               : num 0.05
#>  @ field_method        : chr "BH"
#>  @ cluster_connectivity: num 8
#>  @ cluster_stat        : chr "mass"
```

## Available surrogate families

### Fourier phase randomization

Phase randomization preserves the Fourier amplitude spectrum while
disturbing the temporal phase organization. It is appropriate when the
null is a linear stochastic process characterized by the observed
stationary spectrum. It does not preserve a strongly non-Gaussian
marginal distribution.

### IAAFT

IAAFT iteratively combines spectral projection and rank remapping. The
resulting series preserve the observed values exactly as a multiset
while approximating the observed power spectrum (Schreiber and Schmitz
1996, 2000).

``` r

t <- 1:240
rain <- awi_series(
  t,
  exp(0.6*sin(2*pi*t/24)) + 0.2*cos(2*pi*t/60),
  variable = "rainfall"
)

surr <- awi_iaaft(rain, n = 199, seed = 101)
sort(surr$series[[1]]$value) == sort(rain$value)
surr$diagnostics
```

### AR(p)

The parametric route fits a fixed autoregressive order and simulates
from that model. The order is recorded in `AwiNullSpec`; it is never
selected silently by the inference function.

### Moving and stationary block bootstrap

Block resampling avoids committing to a low-order AR model while
preserving local temporal dependence. The stationary bootstrap uses
geometrically distributed block lengths with a specified expectation
(Politis and Romano 1994).

``` r

block_null <- awi_null_spec(
  method = "stationary_block",
  n_surrogates = 499,
  block_length = 12,
  seed = 301
)
```

### Block permutation and circular shift

Block permutation is a constrained permutation that preserves
within-block ordering. Circular shift preserves the observed series
exactly up to a cyclic displacement and can be useful when the
scientific null is loss of alignment between otherwise unchanged
processes.

## Reproducibility

An explicit seed is locally scoped. The package restores the caller’s
RNG state after generating surrogates with that explicit seed. When
`seed = NA`, the current RNG stream is intentionally used and advanced.
Each surrogate retains the original time coordinates and metadata plus
its surrogate method and replicate number.

## Choosing among nulls

Null models are not interchangeable. A useful workflow is to document,
before examining significance maps:

1.  which temporal properties should be preserved under the null;
2.  whether stationarity is plausible;
3.  whether the marginal distribution is scientifically meaningful;
4.  whether long-memory or seasonal structure requires block or richer
    models;
5.  the number of surrogates required for the intended p-value
    resolution.

With `B` surrogates, the smallest Monte Carlo p-value used by this
package is

``` math
p_{\min}=\frac{1}{B+1}.
```

Thus 19 surrogates are only the mathematical minimum for reaching 0.05.
Final scientific analyses should generally use a larger ensemble and
report `B`.

## Non-stationary caution

Stationary Fourier surrogates can be inadequate for time-varying
spectra. Chavez and Cazelles demonstrated that surrogate design
materially changes the detection of transient wavelet coherence and
explicitly combined surrogate inference with FDR control (Chavez and
Cazelles 2019). Version 0.3.0 records this limitation; non-stationary
wavelet-domain surrogate generators remain a future extension rather
than being approximated under an inaccurate label.

## References

Chavez, Mario, and Bernard Cazelles. 2019. “Detecting Dynamic Spatial
Correlation Patterns with Generalized Wavelet Coherence and
Non-Stationary Surrogate Data.” *Scientific Reports* 9: 7389.
<https://doi.org/10.1038/s41598-019-43571-2>.

Politis, Dimitris N., and Joseph P. Romano. 1994. “The Stationary
Bootstrap.” *Journal of the American Statistical Association* 89 (428):
1303–13. <https://doi.org/10.1080/01621459.1994.10476870>.

Schreiber, Thomas, and Andreas Schmitz. 1996. “Improved Surrogate Data
for Nonlinearity Tests.” *Physical Review Letters* 77 (4): 635–38.
<https://doi.org/10.1103/PhysRevLett.77.635>.

Schreiber, Thomas, and Andreas Schmitz. 2000. “Surrogate Time Series.”
*Physica D: Nonlinear Phenomena* 142 (3-4): 346–82.
<https://doi.org/10.1016/S0167-2789(00)00043-9>.

Torrence, Christopher, and Gilbert P. Compo. 1998. “A Practical Guide to
Wavelet Analysis.” *Bulletin of the American Meteorological Society* 79
(1): 61–78.
<https://doi.org/10.1175/1520-0477(1998)079%3C0061:APGTWA%3E2.0.CO;2>.
