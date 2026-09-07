# Package index

## Package overview

- [`agriWaveInfer`](https://wep69.github.io/agriWaveInfer/reference/agriWaveInfer-package.md)
  [`agriWaveInfer-package`](https://wep69.github.io/agriWaveInfer/reference/agriWaveInfer-package.md)
  : agriWaveInfer: Multiscale Statistical Inference for Agroclimatic
  Systems

## S7 scientific contracts

- [`awi_design()`](https://wep69.github.io/agriWaveInfer/reference/awi_design.md)
  : Create a formal scientific design
- [`awi_wavelet_spec()`](https://wep69.github.io/agriWaveInfer/reference/awi_wavelet_spec.md)
  : Create a formal wavelet specification
- [`awi_condition_spec()`](https://wep69.github.io/agriWaveInfer/reference/awi_condition_spec.md)
  : Numerical conditioning specification for multivariate wavelet
  inference
- [`awi_null_spec()`](https://wep69.github.io/agriWaveInfer/reference/awi_null_spec.md)
  : Specify a surrogate null model and field-aware inference policy
- [`awi_driver_spec()`](https://wep69.github.io/agriWaveInfer/reference/awi_driver_spec.md)
  : Specify a driver-attribution and parsimony policy
- [`awi_phase_spec()`](https://wep69.github.io/agriWaveInfer/reference/awi_phase_spec.md)
  : Specify phase and lead-lag interpretation rules
- [`awi_phenology_spec()`](https://wep69.github.io/agriWaveInfer/reference/awi_phenology_spec.md)
  : Specify phenology alignment rules
- [`awi_propagation_spec()`](https://wep69.github.io/agriWaveInfer/reference/awi_propagation_spec.md)
  : Specify multiscale propagation interpretation rules
- [`awi_network_spec()`](https://wep69.github.io/agriWaveInfer/reference/awi_network_spec.md)
  : Specify rules for time-frequency network construction
- [`awi_spatial_spec()`](https://wep69.github.io/agriWaveInfer/reference/awi_spatial_spec.md)
  : Specify memory-aware spatial wavelet execution
- [`awi_workflow_spec()`](https://wep69.github.io/agriWaveInfer/reference/awi_workflow_spec.md)
  : Specify publication-oriented workflow behavior

## Data contracts and design

- [`awi_series()`](https://wep69.github.io/agriWaveInfer/reference/awi_series.md)
  [`awi_panel()`](https://wep69.github.io/agriWaveInfer/reference/awi_series.md)
  : Create scientific time-series and panel carriers
- [`awi_design()`](https://wep69.github.io/agriWaveInfer/reference/awi_design.md)
  : Create a formal scientific design
- [`awi_wavelet_spec()`](https://wep69.github.io/agriWaveInfer/reference/awi_wavelet_spec.md)
  : Create a formal wavelet specification
- [`awi_align()`](https://wep69.github.io/agriWaveInfer/reference/awi_align.md)
  : Align scientific series
- [`awi_validate()`](https://wep69.github.io/agriWaveInfer/reference/awi_validate.md)
  : Validate agriWaveInfer scientific objects
- [`awi_capabilities()`](https://wep69.github.io/agriWaveInfer/reference/awi_capabilities.md)
  [`awi_doctor()`](https://wep69.github.io/agriWaveInfer/reference/awi_capabilities.md)
  : Inspect computational capabilities and preflight an analysis

## Wavelet coherence

- [`awi_cwt()`](https://wep69.github.io/agriWaveInfer/reference/awi_wavelet.md)
  [`awi_xwt()`](https://wep69.github.io/agriWaveInfer/reference/awi_wavelet.md)
  [`awi_wtc()`](https://wep69.github.io/agriWaveInfer/reference/awi_wavelet.md)
  : Unified continuous, cross, and coherence wavelet interfaces
- [`awi_condition_spec()`](https://wep69.github.io/agriWaveInfer/reference/awi_condition_spec.md)
  : Numerical conditioning specification for multivariate wavelet
  inference
- [`awi_pwc()`](https://wep69.github.io/agriWaveInfer/reference/awi_pwc.md)
  [`awi_partial()`](https://wep69.github.io/agriWaveInfer/reference/awi_pwc.md)
  : Improved partial wavelet coherence with one or more controls
- [`awi_mwc()`](https://wep69.github.io/agriWaveInfer/reference/awi_mwc.md)
  : Multiple wavelet coherence with optional field-aware inference
- [`awi_mwc_compare()`](https://wep69.github.io/agriWaveInfer/reference/awi_mwc_compare.md)
  : Differential comparison of multiple wavelet coherence
  implementations

## Inference

- [`awi_null_spec()`](https://wep69.github.io/agriWaveInfer/reference/awi_null_spec.md)
  : Specify a surrogate null model and field-aware inference policy
- [`awi_surrogate()`](https://wep69.github.io/agriWaveInfer/reference/awi_surrogate.md)
  [`awi_phase_randomize()`](https://wep69.github.io/agriWaveInfer/reference/awi_surrogate.md)
  [`awi_iaaft()`](https://wep69.github.io/agriWaveInfer/reference/awi_surrogate.md)
  [`awi_block_bootstrap()`](https://wep69.github.io/agriWaveInfer/reference/awi_surrogate.md)
  : Generate surrogate agroclimatic time series
- [`awi_fdr()`](https://wep69.github.io/agriWaveInfer/reference/awi_fdr.md)
  : Control false discoveries over a time-frequency p-value field
- [`awi_cluster_test()`](https://wep69.github.io/agriWaveInfer/reference/awi_cluster_test.md)
  : Cluster-based inference on a scale-by-time statistic field

## Drivers

- [`awi_driver_spec()`](https://wep69.github.io/agriWaveInfer/reference/awi_driver_spec.md)
  : Specify a driver-attribution and parsimony policy
- [`awi_awc()`](https://wep69.github.io/agriWaveInfer/reference/awi_driver_metrics.md)
  [`awi_posc()`](https://wep69.github.io/agriWaveInfer/reference/awi_driver_metrics.md)
  : Summarize coherence over an explicit time-frequency domain
- [`awi_gain_loss()`](https://wep69.github.io/agriWaveInfer/reference/awi_gain_loss.md)
  : Quantify incremental gain or loss in multiscale coherence summaries
- [`awi_driver_screen()`](https://wep69.github.io/agriWaveInfer/reference/awi_driver_selection.md)
  [`awi_coherence_elimination()`](https://wep69.github.io/agriWaveInfer/reference/awi_driver_selection.md)
  [`awi_mwc_select()`](https://wep69.github.io/agriWaveInfer/reference/awi_driver_selection.md)
  : Screen and select multiscale drivers with explicit parsimony rules

## Phase and direction

- [`awi_phase_spec()`](https://wep69.github.io/agriWaveInfer/reference/awi_phase_spec.md)
  : Specify phase and lead-lag interpretation rules
- [`awi_phase()`](https://wep69.github.io/agriWaveInfer/reference/awi_phase.md)
  [`awi_phase_lag()`](https://wep69.github.io/agriWaveInfer/reference/awi_phase.md)
  [`awi_lag_band()`](https://wep69.github.io/agriWaveInfer/reference/awi_phase.md)
  [`awi_lead_map()`](https://wep69.github.io/agriWaveInfer/reference/awi_phase.md)
  : Extract phase and convert it to multiscale lead-lag summaries
- [`awi_directionality()`](https://wep69.github.io/agriWaveInfer/reference/awi_directionality.md)
  [`awi_predictive_direction()`](https://wep69.github.io/agriWaveInfer/reference/awi_directionality.md)
  : Summarize phase direction and predictive precedence without causal
  labeling
- [`awi_band_granger()`](https://wep69.github.io/agriWaveInfer/reference/awi_band_granger.md)
  : Experimental band-limited Granger-style predictive precedence
  diagnostic

## Phenology and propagation

- [`awi_phenology_spec()`](https://wep69.github.io/agriWaveInfer/reference/awi_phenology_spec.md)
  : Specify phenology alignment rules
- [`awi_crop_stage()`](https://wep69.github.io/agriWaveInfer/reference/awi_crop_stage.md)
  [`awi_stage_align()`](https://wep69.github.io/agriWaveInfer/reference/awi_crop_stage.md)
  : Create a crop-stage calendar and align a time series to it
- [`awi_stage_coherence()`](https://wep69.github.io/agriWaveInfer/reference/awi_stage_coherence.md)
  : Summarize wavelet coherence by phenological stage
- [`awi_propagation_spec()`](https://wep69.github.io/agriWaveInfer/reference/awi_propagation_spec.md)
  : Specify multiscale propagation interpretation rules
- [`awi_drought_chain()`](https://wep69.github.io/agriWaveInfer/reference/awi_drought_chain.md)
  : Define and harmonize an ordered drought-propagation chain
- [`awi_propagation()`](https://wep69.github.io/agriWaveInfer/reference/awi_propagation.md)
  [`awi_propagation_lag()`](https://wep69.github.io/agriWaveInfer/reference/awi_propagation.md)
  : Estimate sequential multiscale propagation and descriptive
  cumulative lag

## Networks

- [`awi_network_spec()`](https://wep69.github.io/agriWaveInfer/reference/awi_network_spec.md)
  : Specify rules for time-frequency network construction
- [`awi_network()`](https://wep69.github.io/agriWaveInfer/reference/awi_network.md)
  [`awi_dynamic_network()`](https://wep69.github.io/agriWaveInfer/reference/awi_network.md)
  [`awi_scale_network()`](https://wep69.github.io/agriWaveInfer/reference/awi_network.md)
  [`awi_edge_persistence()`](https://wep69.github.io/agriWaveInfer/reference/awi_network.md)
  [`awi_network_compare()`](https://wep69.github.io/agriWaveInfer/reference/awi_network.md)
  : Construct and compare time-frequency wavelet-coherence networks

## Spatial

- [`awi_spatial_spec()`](https://wep69.github.io/agriWaveInfer/reference/awi_spatial_spec.md)
  : Specify memory-aware spatial wavelet execution
- [`awi_spatial_coherence()`](https://wep69.github.io/agriWaveInfer/reference/awi_spatial.md)
  [`awi_coherence_map()`](https://wep69.github.io/agriWaveInfer/reference/awi_spatial.md)
  [`awi_driver_map()`](https://wep69.github.io/agriWaveInfer/reference/awi_spatial.md)
  [`awi_lag_map()`](https://wep69.github.io/agriWaveInfer/reference/awi_spatial.md)
  : Spatial wavelet coherence, dominant-driver, and lag maps

## Workflows and publication

- [`awi_workflow_spec()`](https://wep69.github.io/agriWaveInfer/reference/awi_workflow_spec.md)
  : Specify publication-oriented workflow behavior
- [`awi_workflow()`](https://wep69.github.io/agriWaveInfer/reference/awi_workflow.md)
  : Run an auditable multiscale analysis workflow
- [`awi_report()`](https://wep69.github.io/agriWaveInfer/reference/awi_publication.md)
  [`awi_reproducibility_bundle()`](https://wep69.github.io/agriWaveInfer/reference/awi_publication.md)
  [`awi_publication_check()`](https://wep69.github.io/agriWaveInfer/reference/awi_publication.md)
  : Publication and reproducibility utilities
