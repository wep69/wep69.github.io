# Specify multiscale propagation interpretation rules

Creates an auditable contract for interpreting ordered multiscale
propagation chains.

## Usage

``` r
awi_propagation_spec(require_significance = FALSE, min_coherence = 0.4,
  mask_coi = TRUE, min_resultant_length = 0.5,
  min_direction_persistence = 0.6, allow_reverse = FALSE,
  lag_summary = c("phase_at_representative_period", "weighted_median"),
  nrands = 199L)
```

## Arguments

- require_significance:

  Require the underlying WTC cells to satisfy the stored significance
  field before phase summaries are calculated.

- min_coherence:

  Minimum squared coherence used in phase filtering.

- mask_coi:

  Exclude cone-of-influence-invalid cells.

- min_resultant_length:

  Minimum circular concentration required for an edge-level direction.

- min_direction_persistence:

  Minimum weighted fraction supporting the expected edge direction.

- allow_reverse:

  Whether a stable reverse relation may still be flagged as supported.
  The default is `FALSE`.

- lag_summary:

  Which band-level lag summary is used for edge and cumulative tables.

- nrands:

  Number of backend randomizations used if significance is requested.

## Value

An S7 object of class `AwiPropagationSpec`.

## Details

The default is descriptive rather than significance-filtered. A
supported edge requires adequate circular concentration and directional
persistence in the expected order. Propagation is reported as
scale-dependent temporal precedence, not as a causal effect.
