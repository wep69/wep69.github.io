# Dynamic Wavelet Networks and Edge Persistence

## Networks can change without changing the node set

A teleconnection may dominate for several decades and then weaken, while
soil-moisture or temperature coupling becomes stronger. A single
adjacency matrix averages over such changes. Dynamic wavelet-coherence
networks retain the same variables but summarize the pairwise
time-frequency fields over consecutive windows.

## Reuse pairwise wavelet fields

``` r

dyn <- awi_dynamic_network(
  series = series,
  period_range = c(24, 72),
  window_size = 120,
  step = 24,
  network_spec = awi_network_spec(
    min_edge_weight = 0.35,
    min_persistence = 0.20
  )
)
```

The wavelet transforms are not recomputed independently for every
window. Pairwise fields are calculated once and then summarized within
each declared time window. This reduces unnecessary computation and
keeps scale definitions identical across snapshots.

## Edge persistence

``` r

ep <- awi_edge_persistence(dyn)
ep$table
```

For a dynamic network, edge persistence is

``` math
EP_{ij}=\frac{1}{K}\sum_{k=1}^{K}I\{A_{ij}^{(k)}>0\},
```

where $`K`$ is the number of snapshots. This distinguishes a transient
strong connection from a relationship that repeatedly appears through
time.

## Compare networks directly

``` r

cmp <- awi_network_compare(
  dyn$snapshots[[1]],
  dyn$snapshots[[length(dyn$snapshots)]]
)
cmp
```

The comparison reports edge-set Jaccard similarity, correlation and RMSE
of edge weights, and rank correlation of node degrees. These are
descriptive network-comparison quantities, not formal evidence that a
climate mechanism changed.

## Compare scales

``` r

scales <- awi_scale_network(
  series = series,
  bands = list(
    seasonal = c(8, 20),
    interannual = c(32, 64)
  ),
  network_spec = awi_network_spec(min_edge_weight = 0.35)
)

awi_network_compare(
  scales$networks$seasonal,
  scales$networks$interannual
)
```

Scale-specific networks have precedent in wavelet-coherence network
methodology (Lim and Park 2024). In `agriWaveInfer`, every layer retains
the period band and edge rule that generated it, so a graph remains
traceable to its underlying scientific domain.

Lim, Gyuchang, and Jong-Jin Park. 2024. “Examining Modulations of
Internal Tides Within an Anticyclonic Eddy Using a Wavelet-Coherence
Network Approach.” *Applied Sciences* 14 (3): 1001.
<https://doi.org/10.3390/app14031001>.
