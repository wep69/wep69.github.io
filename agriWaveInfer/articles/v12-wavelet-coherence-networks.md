# Wavelet-Coherence Networks for Agroclimatic Systems

## Why move from coherence plots to networks?

Pairwise coherence is useful when the scientific question contains one
response and one driver. Agroclimatic systems, however, often contain
many potentially coupled variables: ENSO, PDO, rainfall, temperature,
soil moisture, vegetation indices and yield. A collection of dozens of
pairwise plots becomes difficult to synthesize. Recent wavelet-coherence
network work shows that scale-specific networks can represent which
oscillatory components are connected at a chosen period and how network
structure changes through time (Lim and Park 2024).

`agriWaveInfer` therefore treats the network as a summary layer above
validated pairwise fields, not as a replacement for the underlying
wavelet inference.

## Define the edge rule explicitly

``` r

ns <- awi_network_spec(
  edge_metric = "awc",
  min_edge_weight = 0.35,
  min_coherence = 0.50,
  min_persistence = 0.20,
  require_significance = FALSE,
  directed = FALSE,
  mask_coi = TRUE
)
```

The edge weight, cell-level coherence threshold, persistence
requirement, COI policy and valid-domain requirement are all stored in
the specification. An edge cannot be reconstructed from a figure alone.

## Build an agroclimatic network

``` r

series <- list(
  ENSO = enso,
  rainfall = rainfall,
  soil_moisture = soil_moisture,
  NDVI = ndvi,
  yield = yield
)

net <- awi_network(
  series = series,
  period_range = c(24, 72),
  network_spec = ns
)

net$edges
net$adjacency
net$node_metrics
```

Pairwise WTC is computed once. AWC, PoSC when requested, persistence,
valid coverage and phase summaries remain attached to the edge table.

## Direction is phase precedence, not causality

``` r

ns_dir <- awi_network_spec(
  directed = TRUE,
  min_edge_weight = 0.35,
  min_resultant_length = 0.60
)

net_dir <- awi_network(
  series = series,
  period_range = c(24, 72),
  network_spec = ns_dir,
  phase_spec = awi_phase_spec(require_significance = FALSE)
)
```

When direction is requested, positive phase means the first node leads
the second under the package convention. Near-zero phase can become
reciprocal or be dropped. Anti-phase ambiguity and excessive circular
dispersion prevent directional interpretation. The returned object
always records `causal_effect = FALSE`.

## Scale-specific agricultural interpretation

A network at 8–20 months can emphasize seasonal water-vegetation
coupling, while a 32–64 month network may emphasize teleconnection
structure. This distinction is important because recent agricultural
work shows that climate-yield relationships differ substantially by time
scale (Sankaran et al. 2026).

Lim, Gyuchang, and Jong-Jin Park. 2024. “Examining Modulations of
Internal Tides Within an Anticyclonic Eddy Using a Wavelet-Coherence
Network Approach.” *Applied Sciences* 14 (3): 1001.
<https://doi.org/10.3390/app14031001>.

Sankaran, Adarsh, Fathima Fazal, Aleena Johnson, Venkataramana Sridhar,
Divyasree Prasad, and Aakhila Fathima Abdul Rasheed. 2026.
“Wavelet-Based Assessment of Climate Oscillation and Meteorological
Influences on Cereal Yield Variability Across India’s Agro-Climatic
Zones.” *Theoretical and Applied Climatology* 157: 147.
<https://doi.org/10.1007/s00704-026-06085-2>.
