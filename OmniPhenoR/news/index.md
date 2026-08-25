# Changelog

## OmniPhenoR 1.0.0

### Stable integrated release

- Consolidates all capabilities developed from 0.1.0 through 0.5.0.
- Preserves the complete public-export union observed across the five
  development generations.
- Restores eight longitudinal exports that were present in 0.4.0 but
  accidentally absent from the 0.5.0 development source:
  [`pheno_correlation_structures()`](https://wep69.github.io/OmniPhenoR/reference/pheno_correlation_structures.md),
  [`pheno_curve_compare()`](https://wep69.github.io/OmniPhenoR/reference/pheno_curve_compare.md),
  [`pheno_event()`](https://wep69.github.io/OmniPhenoR/reference/pheno_event.md),
  [`pheno_event_response()`](https://wep69.github.io/OmniPhenoR/reference/pheno_event_response.md),
  [`pheno_growth()`](https://wep69.github.io/OmniPhenoR/reference/pheno_growth.md),
  [`pheno_model_diagnostics()`](https://wep69.github.io/OmniPhenoR/reference/pheno_model_diagnostics.md),
  [`pheno_phenology()`](https://wep69.github.io/OmniPhenoR/reference/pheno_phenology.md),
  and
  [`pheno_series_qc()`](https://wep69.github.io/OmniPhenoR/reference/pheno_series_qc.md).
- Restores `print.pheno_growth`, `print.pheno_repeated_fit`, and
  `print.pheno_functional`.
- Uses the richer 0.4.0 longitudinal interface as the stable canonical
  API where the 0.5.0 development branch had introduced incompatible
  simplified signatures.
- Retains non-conflicting 0.5.0 convenience entry points including
  [`pheno_gdd()`](https://wep69.github.io/OmniPhenoR/reference/pheno_gdd.md),
  [`pheno_growth_fit()`](https://wep69.github.io/OmniPhenoR/reference/pheno_growth_fit.md),
  [`pheno_growth_predict()`](https://wep69.github.io/OmniPhenoR/reference/pheno_growth_predict.md),
  [`pheno_audpc()`](https://wep69.github.io/OmniPhenoR/reference/pheno_audpc.md),
  [`pheno_interpolate()`](https://wep69.github.io/OmniPhenoR/reference/pheno_interpolate.md),
  [`pheno_time_gaps()`](https://wep69.github.io/OmniPhenoR/reference/pheno_time_gaps.md),
  [`pheno_time_since_event()`](https://wep69.github.io/OmniPhenoR/reference/pheno_time_since_event.md),
  [`pheno_missing_time()`](https://wep69.github.io/OmniPhenoR/reference/pheno_missing_time.md),
  [`pheno_time_validate_model()`](https://wep69.github.io/OmniPhenoR/reference/pheno_time_validate_model.md),
  and
  [`pheno_curve_distance()`](https://wep69.github.io/OmniPhenoR/reference/pheno_curve_distance.md).
- Consolidates the 0.5.0 experiment, catalog, storage, manifest, MIAPPE,
  BrAPI, FAIR, privacy, lineage, snapshot, and reproducibility-bundle
  infrastructure on top of the 0.4.0 longitudinal core.
- Expands the implementation registry to 240 blocks: 32 from 0.1.0, 40
  from 0.2.0, 50 from 0.3.0, 58 from 0.4.0, and 60 from 0.5.0.
- Adds the integrated `v43-omniphenor-1.0-integrated-workflow.Rmd`
  vignette.
- Maintains the precompiled-vignette installation policy.
- Adds an explicit API compatibility ledger and integrated cross-version
  validation report.

#### Fixes during local validation (2026-08-24)

- [`pheno_diff()`](https://wep69.github.io/OmniPhenoR/reference/pheno_diff.md)
  compared a nonexistent `file_id` column of the manifest, so every
  comparison silently reported zero changes; it is now keyed on
  registered file paths with SHA-256 change detection.
- [`pheno_snapshot()`](https://wep69.github.io/OmniPhenoR/reference/pheno_snapshot.md)
  now freezes the current on-disk state (`refresh = TRUE`), making
  snapshots genuine point-in-time audit artifacts and enabling tampering
  detection through
  [`pheno_diff()`](https://wep69.github.io/OmniPhenoR/reference/pheno_diff.md).
- Fixed a parse error in the consolidation test caused by use of the
  reserved word `function` as an R column accessor.
- Updated the block-registry test to the consolidated expectation of 240
  blocks (the previous assertion reflected the 0.2.0-era registry of 72
  blocks).
- Added a regression test proving that modified registered files are
  detected by snapshot diffing.
- Hardened `print.pheno_series()` so printing a column-subset series
  (which loses the column-spec attribute through `[.data.frame`) no
  longer errors; unavailable summary lines are skipped.
- Fixed the integrated 1.0 workflow vignette, which assumed a
  long-format `drought_recovery` table; it now reshapes the wide source
  data explicitly before building the series.

#### Physical image fixtures and I/O hardening (2026-08-24)

- Added ten deterministic synthetic image fixtures
  (`inst/extdata/images/`) with ground-truth tables
  (`inst/extdata/truth/`): RGB/gray photographs, leaf and lesion masks
  with exact areas, a 12-plant canopy scene with detection truth, YOLO
  round-trip text, 16-bit instance labels, an exact physical scale bar
  and a six-patch RGB reference chart. Generation and verification
  scripts live in `data-raw/image-fixtures/`.
- Added `tests/testthat/test-image-file-fixtures.R`, exercising physical
  file I/O end-to-end without optional backends; `magick` and `terra`
  paths are guarded by `skip_if_not_installed()`.
- Hardened
  [`pheno_read()`](https://wep69.github.io/OmniPhenoR/reference/pheno_read.md)
  (magick engine): some ImageMagick 6 builds return corrupted or
  inconsistently scaled buffers from `image_data()` for plain 8-bit PNG
  sources, silently corrupting pixel values. Decoding now goes through
  `image_raster()`, which reproduces the file bit-exactly (verified
  against independent libpng decoding).
- Fixed
  [`pheno_detection_to_sf()`](https://wep69.github.io/OmniPhenoR/reference/pheno_detection_to_sf.md)
  for current sf versions: the former `crs = NA` default is rejected by
  [`sf::st_sfc()`](https://r-spatial.github.io/sf/reference/sfc.html)
  input validation; the default now resolves to an unspecified CRS
  (`NA_character_`) while explicit EPSG codes and CRS strings keep
  working.
- Qualified
  [`utils::capture.output()`](https://rdrr.io/r/utils/capture.output.html)
  calls and wrapped one over-long documentation example line to satisfy
  CRAN check NOTEs.

This source snapshot is not release-frozen until the exact source passes
local `roxygen2`, `testthat`, complete R/knitr vignette rendering,
`R CMD build`, `R CMD check`, and `R CMD check --as-cran`.

## OmniPhenoR 0.4.0

### Longitudinal phenotyping and dynamic agronomic traits

- Preserves the classical, native-R deep-learning, and external
  computer-vision layers from 0.1.0–0.3.0 while adding an explicit
  longitudinal phenotype layer.
- Expands the implementation registry from 122 to 180 blocks.
- Adds
  [`pheno_series()`](https://wep69.github.io/OmniPhenoR/reference/pheno_series.md),
  validation/QC, common-grid alignment, explicit time origins and
  [`pheno_thermal_time()`](https://wep69.github.io/OmniPhenoR/reference/pheno_thermal_time.md).
- Adds growth-model workflows, AGR, RGR, AUC, disease-progress/AUDPC,
  senescence, phenological landmarks, changepoints and derivatives.
- Adds repeated-measures interfaces with transparent `lm` fallback and
  optional `nlme`/`lme4`, correlation-structure inspection, model
  comparison, contrasts and diagnostics.
- Adds functional trajectory construction, FPCA, trajectory clustering,
  within-subject/lagged trait correlations, weather joins and
  environmental windows.
- Adds experimental-event, stress/recovery, cluster-bootstrap,
  sampling-sensitivity, dynamic-trait-table and longitudinal plotting
  helpers.
- Adds deterministic growth, disease, drought/recovery and weather
  teaching data.
- Adds ten focused instructional vignettes (`v23`–`v32`) and extends the
  long `v00` tutorial with an integrated
  image-to-trajectory-to-inference section.
- Distributes 33 precompiled vignette HTML artifacts for fast GitHub
  installation with `build_vignettes = FALSE`. Heavy computations remain
  separate from ordinary documentation access.
- Adds deterministic compact temporal reference artifacts plus an R
  regeneration script.
- Adds metadata-verified references for growth curves, growing degree
  days, longitudinal/mixed modeling, functional data, changepoints,
  smoothing, AUDPC, field phenotyping and phenomics. Every promoted
  0.4.0 scientific reference has at least two recorded verification
  sources.

#### Validation status

The 0.4.0 snapshot was assembled in an environment without R. Static
source/documentation/reference checks and precompiled Pandoc previews
are provided, but this version is **not frozen**. Before distribution,
run the complete protocol in `LOCAL_VALIDATION.md`: regenerate roxygen
documentation, rerender all 33 vignettes with R/knitr, execute tests,
build and install the exact tarball, verify installed vignette
availability, complete `R CMD check --as-cran`, and record the final
SHA-256.

## OmniPhenoR 0.3.0

### Interoperable computer vision and precompiled documentation

- Adds an optional Python capability layer that is non-initializing by
  default and never installs Python packages during package load,
  checks, or ordinary vignette rendering.
- Adds
  [`pheno_python_status()`](https://wep69.github.io/OmniPhenoR/reference/pheno_python_status.md),
  [`pheno_python_packages()`](https://wep69.github.io/OmniPhenoR/reference/pheno_python_packages.md),
  [`pheno_python_require()`](https://wep69.github.io/OmniPhenoR/reference/pheno_python_require.md),
  [`pheno_python_environment()`](https://wep69.github.io/OmniPhenoR/reference/pheno_python_environment.md),
  [`pheno_python_validate()`](https://wep69.github.io/OmniPhenoR/reference/pheno_python_validate.md),
  and
  [`pheno_environment_report()`](https://wep69.github.io/OmniPhenoR/reference/pheno_environment_report.md).
- Adds a backend registry with explicit language, package, task, output
  class, status, and licensing metadata.
- Adds canonical `pheno_detection` and `pheno_instances` objects plus
  object detection, instance segmentation, coordinate conversion,
  NMS/merge, and validation metrics.
- Adds opt-in adapters for PlantCV, Ultralytics YOLO, LeafMachine2,
  custom Python/external models, and external command execution.
  Third-party code and model weights are not bundled.
- Adds COCO-style and YOLO bounding-box interchange plus annotation QC.
- Adds biological identity, object-to-plant linkage, trait aggregation,
  ensemble, cross-engine validation, and model-comparison helpers.
- Extends
  [`pheno_morphology()`](https://wep69.github.io/OmniPhenoR/reference/pheno_morphology.md)
  to accept `pheno_instances` while preserving the existing mask
  workflow.
- Adds model-cache and explicit-download helpers with SHA-256
  verification and license acknowledgement.
- Expands the implementation registry from 72 to 122 blocks.
- Adds ten focused vignettes (`v13`–`v22`) covering Python backends,
  PlantCV, object detection, instance segmentation, YOLO, LeafMachine2,
  annotation formats, object-to-plant aggregation, cross-engine
  validation, and release reproducibility.
- Adopts precompiled vignette distribution: source Rmd remains in
  `vignettes/`, HTML is distributed in `inst/doc/`, compact frozen
  outputs are stored in `inst/extdata/vignette-results/`, and heavy
  regeneration scripts remain in `data-raw/vignette-results/`.
- Adds
  [`pheno_vignettes()`](https://wep69.github.io/OmniPhenoR/reference/pheno_vignettes.md)
  and
  [`pheno_example_result()`](https://wep69.github.io/OmniPhenoR/reference/pheno_example_result.md)
  for installed documentation discovery and frozen teaching outputs.
- Adds metadata-verified references for PlantCV v4, LeafMachine2, YOLO,
  Mask R-CNN, and Microsoft COCO, plus a separate backend-license audit.

#### Validation status

The 0.3.0 source snapshot was assembled in an environment without R.
Static structure, documentation artifacts, source/output hashes, and
non-R checks are provided, but the release is **not frozen**. Before
distribution, run the full local validation protocol, rerender all 23
vignettes with R/knitr, regenerate roxygen documentation, execute all
tests, build the exact tarball, and complete `R CMD check --as-cran`.

## OmniPhenoR 0.2.0

### Model-aware phenotyping release

- Preserves the 32-block 0.1.0 classical core and expands the
  implementation registry to 72 blocks.
- Adds a provenance-rich model registry, model save/load helpers, device
  detection, and deep-learning capability inspection.
- Adds
  [`pheno_dl_dataset()`](https://wep69.github.io/OmniPhenoR/reference/pheno_dl_dataset.md),
  biological group-aware
  [`pheno_dl_split()`](https://wep69.github.io/OmniPhenoR/reference/pheno_dl_split.md),
  and auditable augmentation with explicit protection against
  phenotype-altering photometric transforms.
- Adds compact native R `torch` U-Net and CNN reference models plus
  [`pheno_train()`](https://wep69.github.io/OmniPhenoR/reference/pheno_train.md)
  with explicit seeds, splits, learning history, engine/device metadata,
  and registry integration.
- Adds `pheno_prediction`, unified prediction, neural semantic
  segmentation, neural classification, retained class probabilities, QC,
  runtime, and model provenance.
- Adds conservative `engine = "auto"` behavior to
  [`pheno_segment()`](https://wep69.github.io/OmniPhenoR/reference/pheno_segment.md):
  model-based execution is used only when a model is explicitly
  supplied, and the engine-selection reason is recorded.
- Adds uncertainty, probability calibration, Brier/log-loss/ECE
  diagnostics, segmentation metrics, classical-versus-neural comparison,
  phenotype-level area bias, and benchmarking.
- Adds large-image tiling, overlap-aware merging, tiled prediction, RGB
  validation/comparison/correlation/ranking, texture-grid mapping,
  multiscale texture, stability, and method comparison.
- Adds deterministic validation objects and expands source-tree tests
  from the 0.1.0 baseline to 106 passing tests.
- Adds eight focused instructional vignettes (`v05`-`v12`) while
  retaining and integrating the five 0.1.0 vignettes.
- Adds two-source metadata verification for U-Net, image augmentation,
  dropout-based uncertainty, neural calibration, and machine-learning
  leakage references.

## OmniPhenoR 0.1.0

### Initial integrator release

- Introduces an R-first capability registry and `pheno_project`
  provenance object.
- Implements a 32-block public architecture covering project identity,
  acquisition, calibration, RGB indices, segmentation, morphometry,
  phytopathometry, texture, QC, validation, spatial linkage, workflows,
  graphics, reports, and reference verification.
- Adds 59 named RGB/color descriptors. Formula metadata and scale
  handling are explicit, and the canonical two-channel `NGRDI` is kept
  distinct from the three-channel denominator variant `NGRDI3`.
- Adds classical threshold segmentation, connected-component filtering,
  morphology, and disease-severity tools.
- Adds nine complementary texture families: first-order statistics,
  GLCM/Haralick, GLRLM, local binary patterns, HOG, Gabor filter banks,
  Haar DWT, Laws texture energy, and FFT-domain descriptors.
- Adds optional capability detection for `pliman`, locally installed
  `FIELDimageR`, `terra`, `sf`, `OpenImageR`, `waveslim`, `torch`, and
  `reticulate` without making Python or Julia mandatory.
- Adds deterministic teaching images, truth masks, user-facing
  scientific validation checks, input hashing, audit trails, and
  reproducible report skeletons.
- Adds five instructional vignettes: one long basic-to-advanced tutorial
  plus focused RGB, texture, leaf-health, and validation/reproducibility
  guides.
- Adds a release bibliography and a two-source metadata-verification
  ledger.
- Final source-tree unit-test baseline: 48 passing tests, no failures,
  warnings, or skips on the release-validation machine.
- Final `R CMD check --as-cran` baseline on Windows/R 4.6.0: no ERROR,
  no WARNING, and no package-caused NOTE; CRAN incoming reports only the
  expected administrative `New submission` NOTE for an unpublished
  package.
