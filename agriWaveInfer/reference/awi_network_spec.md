# Specify rules for time-frequency network construction

Creates a validated scientific contract for converting pairwise
wavelet-coherence fields into scale- and time-specific network edges.

## Usage

``` r
awi_network_spec(edge_metric = c("awc", "posc", "persistence"),
  min_edge_weight = 0.3, min_coherence = 0.5, min_persistence = 0.1,
  require_significance = FALSE, min_posc = 0, directed = FALSE,
  min_resultant_length = 0.5,
  synchronous_policy = c("reciprocal", "drop"), mask_coi = TRUE,
  weighting = c("area", "cell"), min_valid_fraction = 0.2)
```

## Arguments

- edge_metric:

  Normalized edge-weight metric: average wavelet coherence (`"awc"`),
  PoSC divided by 100 (`"posc"`), or cell-level persistence
  (`"persistence"`).

- min_edge_weight:

  Minimum normalized edge weight in \[0,1\].

- min_coherence:

  Minimum cell-level coherence used when calculating persistence.

- min_persistence:

  Minimum fraction of valid cells satisfying the cell-level edge rule.

- require_significance:

  Whether network presence requires a compatible significance field.

- min_posc:

  Minimum Percentage of Significant Coherence in \[0,100\] when
  significance is required.

- directed:

  Whether phase information may orient edges.

- min_resultant_length:

  Minimum circular resultant length for accepting phase-based direction.

- synchronous_policy:

  How near-zero phase is handled in a directed network: reciprocal arcs
  or no edge.

- mask_coi:

  Whether to exclude cells outside the cone of influence.

- weighting:

  Domain weighting used by AWC and PoSC.

- min_valid_fraction:

  Minimum valid fraction of the requested time-period domain.

## Value

An S7 `AwiNetworkSpec` object.

## Details

The contract separates an edge's weight from its persistence and
phase-based direction. A directed edge records temporal phase precedence
only and never implies a causal effect.
