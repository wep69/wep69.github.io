# API lifecycle — agriWaveInfer 1.0.0

## Stable API

The 1.0.0 release freezes the public API defined in
`API_FREEZE_1.0.0.md`. Core data contracts, wavelet transforms/adapters,
PWC/MWC, null-model and field inference, driver attribution/selection,
phase/lag, phenology, propagation, networks, spatial inference, workflow
orchestration, reporting, reproducibility bundles, and publication
checks are part of the 1.0 public surface.

## Experimental components within the stable surface

The following remain scientifically experimental even though their
function names are retained for compatibility:

- band-filtered Granger-style predictive precedence;
- directed network interpretation from phase;
- large spatial workflows whose performance depends strongly on optional
  backends and machine resources.

Experimental status means interpretation and implementation details may
improve in 1.x. It does not permit silent causal reinterpretation or
removal without lifecycle documentation.

## Deprecation

No public API is deprecated in 1.0.0. See `API_FREEZE_1.0.0.md` for the
deprecation protocol.
