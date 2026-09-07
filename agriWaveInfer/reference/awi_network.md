# Construct and compare time-frequency wavelet-coherence networks

Builds network representations from pairwise wavelet-coherence fields in
explicitly declared time and period domains.

## Usage

``` r
awi_network(series = NULL, pairwise = NULL, period_range = NULL,
  time_range = NULL, wavelet_spec = awi_wavelet_spec(),
  phase_spec = awi_phase_spec(require_significance = FALSE),
  network_spec = awi_network_spec(), nrands = 300L,
  sig_level = 0.95, quiet = TRUE)

awi_dynamic_network(series = NULL, pairwise = NULL, period_range = NULL,
  window_size, step = window_size, wavelet_spec = awi_wavelet_spec(),
  phase_spec = awi_phase_spec(require_significance = FALSE),
  network_spec = awi_network_spec(), nrands = 300L,
  sig_level = 0.95, quiet = TRUE)

awi_scale_network(series = NULL, pairwise = NULL, bands,
  time_range = NULL, wavelet_spec = awi_wavelet_spec(),
  phase_spec = awi_phase_spec(require_significance = FALSE),
  network_spec = awi_network_spec(), nrands = 300L,
  sig_level = 0.95, quiet = TRUE)

awi_edge_persistence(x)

awi_network_compare(x, y)
```

## Arguments

- series:

  Named list of at least two aligned `awi_series` objects. Pairwise WTC
  is computed once and then reused.

- pairwise:

  Alternative named list of precomputed `awi_wavelet_result` objects.
  Each result must retain its two-node `phase_pair`, or the list element
  must be named `"node1::node2"`.

- period_range:

  Optional two-element period band defining the network layer.

- time_range:

  Optional two-element physical-time window.

- wavelet_spec:

  An `AwiWaveletSpec`.

- phase_spec:

  An `AwiPhaseSpec` controlling phase summaries when direction is
  requested.

- network_spec:

  An `AwiNetworkSpec`.

- nrands:

  Non-negative integer number of backend randomizations when pairwise
  significance is requested.

- sig_level:

  Backend significance level.

- quiet:

  Suppress pairwise progress messages.

- window_size:

  Number of time observations per dynamic-network window.

- step:

  Number of observations between consecutive window starts.

- bands:

  Named list of two-element period ranges for scale-specific networks.

- x:

  For `awi_edge_persistence()`, an `awi_dynamic_network`; for
  `awi_network_compare()`, an `awi_network`.

- y:

  Second `awi_network` for comparison.

## Value

`awi_network()` returns an `awi_network` with an edge table, weighted
adjacency matrix, node metrics, density, pairwise fields, and an
optional igraph object. `awi_dynamic_network()` returns windowed network
snapshots. `awi_scale_network()` returns named period-band layers.
`awi_edge_persistence()` returns edge recurrence across dynamic
snapshots. `awi_network_compare()` returns topology and weight
similarity metrics.

## Details

Edge weights are normalized to \[0,1\]. AWC is used directly, PoSC is
divided by 100, and persistence is the fraction of valid cells
satisfying the cell-level rule. If `directed = TRUE`, phase information
may orient an edge only when circular concentration is sufficient;
anti-phase ambiguity can remove the edge. Near-zero phase can be
represented by reciprocal arcs or dropped. Network direction indicates
phase-based precedence, not causality. Dynamic networks reuse one
pairwise wavelet decomposition and summarize moving windows rather than
recomputing transforms for each window.

## References

Grinsted A, Moore JC, Jevrejeva S (2004). Application of the cross
wavelet transform and wavelet coherence to geophysical time series.
Nonlinear Processes in Geophysics 11:561-566.
doi:10.5194/npg-11-561-2004.

Lim G, Park J-J (2024). Examining Modulations of Internal Tides within
An Anticyclonic Eddy Using a Wavelet-Coherence Network Approach. Applied
Sciences 14(3):1001. doi:10.3390/app14031001.
