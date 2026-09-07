# Specify phase and lead-lag interpretation rules

Creates a validated policy for interpreting phase and lead-lag
information without converting phase precedence into a causal claim.

## Usage

``` r
awi_phase_spec(
  require_significance = TRUE, min_coherence = 0, mask_coi = TRUE,
  weighting = c("coherence", "uniform"), min_resultant_length = 0.5,
  near_zero_phase = pi/12, anti_phase_margin = pi/18
)
```

## Arguments

- require_significance:

  Require cells to satisfy the stored significance field before phase
  interpretation.

- min_coherence:

  Minimum squared coherence accepted for phase interpretation.

- mask_coi:

  Exclude cells outside the cone-of-influence validity region.

- weighting:

  Use coherence or uniform weights for circular band summaries.

- min_resultant_length:

  Minimum circular resultant length required for a directional band
  summary.

- near_zero_phase:

  Absolute phase angle below which timing is classified as near-zero
  lag.

- anti_phase_margin:

  Angular margin around plus or minus pi that is explicitly classified
  as cycle-ambiguous anti-phase.

## Value

An S7 object of class `AwiPhaseSpec`.

## Details

Phase is circular modulo 2\*pi. Version 0.5.0 uses the principal branch
and explicitly marks cells near plus or minus pi as ambiguous.
Directional summaries also require sufficient circular concentration.
