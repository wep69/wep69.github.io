# API freeze — agriWaveInfer 1.0.0

## Status

The 1.0.0 public API is frozen at **73 exported symbols**. No new
methodological domain was added during the 1.0.0 consolidation.

## Stability policy

- Existing stable functions/classes will not be removed or change
  meaning within the 1.x line without a documented deprecation cycle.
- Additive arguments must preserve existing defaults unless a
  scientifically necessary correction is documented.
- Experimental behavior is identified in documentation and may evolve,
  but existing serialized result fields should be migrated explicitly
  when changed.
- Directional, propagation, network, and spatial outputs remain
  association-based and must not be relabeled as causal effects.

## Frozen export set

``` text
AwiConditionSpec
AwiDesign
AwiDriverSpec
AwiNetworkSpec
AwiNullSpec
AwiPhaseSpec
AwiPhenologySpec
AwiPropagationSpec
AwiSpatialSpec
AwiWaveletSpec
AwiWorkflowSpec
awi_align
awi_awc
awi_band_granger
awi_block_bootstrap
awi_capabilities
awi_cluster_test
awi_coherence_elimination
awi_coherence_map
awi_condition_spec
awi_crop_stage
awi_cwt
awi_design
awi_directionality
awi_doctor
awi_driver_map
awi_driver_screen
awi_driver_spec
awi_drought_chain
awi_dynamic_network
awi_edge_persistence
awi_fdr
awi_gain_loss
awi_iaaft
awi_lag_band
awi_lag_map
awi_lead_map
awi_mwc
awi_mwc_compare
awi_mwc_select
awi_network
awi_network_compare
awi_network_spec
awi_null_spec
awi_panel
awi_partial
awi_phase
awi_phase_lag
awi_phase_randomize
awi_phase_spec
awi_phenology_spec
awi_posc
awi_predictive_direction
awi_propagation
awi_propagation_lag
awi_propagation_spec
awi_publication_check
awi_pwc
awi_report
awi_reproducibility_bundle
awi_scale_network
awi_series
awi_spatial_coherence
awi_spatial_spec
awi_stage_align
awi_stage_coherence
awi_surrogate
awi_validate
awi_wavelet_spec
awi_workflow
awi_workflow_spec
awi_wtc
awi_xwt
```

## Compatibility baseline

- R \>= 4.3.0
- S7 \>= 0.2.2
- Scientific transform/network/spatial backends remain optional
  capabilities.

## 1.x deprecation protocol

A public symbol targeted for removal must first be marked deprecated,
documented in NEWS, covered by a migration note, and retained for at
least one minor release unless a critical correctness or safety issue
requires faster action.
