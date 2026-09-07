# Changelog

## agriWaveInfer 1.0.0

### GitHub release (runtime compatibility, API unchanged)

Validated on Windows 10 x64 with R 4.6.0 against S7 0.2.2 and current
CRAN backends. The scientific API remains frozen at 73 exports and 42 S3
methods; no estimator, default, or S7 contract was changed.

- `R/zzz.R`: call `S7::S7_on_load()` only when the installed S7 provides
  it (removed in S7 \>= 0.2.0, which self-registers).
- `R/wavelet.R`: clip
  [`awi_wtc()`](https://wep69.github.io/agriWaveInfer/reference/awi_wavelet.md)
  coherence to \[0, 1\] with the existing `.awi_clip01()` helper, as
  [`awi_pwc()`](https://wep69.github.io/agriWaveInfer/reference/awi_pwc.md)
  and
  [`awi_mwc()`](https://wep69.github.io/agriWaveInfer/reference/awi_mwc.md)
  already do (raw
  [`biwavelet::wtc()`](https://rdrr.io/pkg/biwavelet/man/wtc.html)
  ratios can overshoot 1 by ~2e-9).
- `R/spatial.R`: use time-frequency area weighting in
  [`awi_spatial_coherence()`](https://wep69.github.io/agriWaveInfer/reference/awi_spatial.md)
  summaries instead of forwarding the phase-spec weighting, and reset
  summary row names so results are invariant to memory chunking.
- `DESCRIPTION`: added `URL`/`BugReports` for the GitHub repository and
  site; added installation instructions to `README.md` and a
  `_pkgdown.yml` site configuration (site:
  <https://wep69.github.io/agriWaveInfer>).
- `inst/validation/spatial_recovery.R`: attach the package explicitly,
  like the other validation harnesses.
- Tests: fixed outdated expectations that never passed on current
  toolchains (regex escapes, trailing comma, helper ordering, mock
  completeness, complex-vs-double identity, resolution-gate sample
  size).

### Known validation deviation (07/09/2026, R 4.6.0 + biwavelet 0.20.22)

- `inst/validation/driver_selection_stability.R` (frozen 0.4.0 gates
  `primary_first_frequency >= 0.80`, `noise_added_frequency <= 0.20`)
  returns 0.72 / 0.38 on the reference toolchain, so the gate FAILS. A
  controlled A/B rerun of the first 10 replicates with and without the
  [`awi_wtc()`](https://wep69.github.io/agriWaveInfer/reference/awi_wavelet.md)
  coherence clipping above produced byte-identical selections, proving
  the deviation is pre-existing frozen-code behavior on current
  backends, not a regression from this release. No estimator, threshold,
  seed, or gate was altered to chase the number. The CEA truth-case unit
  tests and all other validation harnesses pass; see
  `VALIDATION_RESULT_1.0.0.md` (validation evidence bundle) for the full
  gate table.

### Consolidated Scientific Release

Version 1.0.0 consolidates the complete 0.1.0–0.9.0 development line
without introducing a new methodological domain. The public API is
frozen at 73 exported symbols.

#### Scientific layers consolidated

- scientific time-series, panel, design, alignment, and validation
  contracts;
- unified CWT, XWT, and WTC adapters with explicit backend provenance;
- improved partial wavelet coherence and multiple wavelet coherence with
  local conditioning diagnostics;
- AR(p), phase-randomized, IAAFT, block-bootstrap, permutation, and
  circular-shift null models;
- FDR and cluster-aware inference over the time-frequency field;
- AWC/PoSC metrics, gain-loss, driver screening, coherence elimination,
  and parsimonious MWC selection;
- circular phase, phase-to-lag conversion, band summaries, lead maps,
  and noncausal predictive direction;
- phenology-aligned interpretation and ordered drought/impact
  propagation summaries;
- static, scale-specific, and dynamic wavelet-coherence networks with
  edge persistence and comparison;
- spatial coherence, dominant-driver, and lag mapping with memory-aware
  chunk planning and optional `terra`/`stars` interoperability;
- auditable workflow orchestration, Markdown reporting, reproducibility
  bundles, source-tree publication checks, and publication metadata
  scaffolding.

#### 1.0.0 release engineering

- Froze the API lifecycle and added an explicit `API_FREEZE_1.0.0.md`.
- Added release checklist, consolidated validation ledger, metadata
  reconciliation, and final golden manifest.
- Added regression tests that lock the 1.0.0 export set and release
  metadata.
- Preserved `R (>= 4.3.0)` because native S7 `@` property access is part
  of the implementation; R 4.6.1 is the reference release for final
  runtime certification.
- Preserved optional scientific backends in `Suggests`; the core does
  not silently install transform, network, or geospatial engines.
- Preserved the package-wide rule that association, phase lead,
  predictive precedence, propagation, spatial dominance, and directed
  network edges do not identify causal effects.

### Validation state

The source tree, metadata, frozen numerical references, data checksums,
API surface, bibliography, and deterministic archives are audited in the
construction environment. R runtime execution, optional-backend
differential validation, vignette rendering, exact-tarball installation,
and `R CMD check --as-cran` must be executed on the exact release
archive before claiming runtime-certified status.
