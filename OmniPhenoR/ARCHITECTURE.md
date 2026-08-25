# OmniPhenoR 0.4.0 architecture

## Governing principle

OmniPhenoR is an R-first scientific orchestration layer.
Image-processing backends are capabilities rather than competing public
APIs, and repeated image measurements do not redefine the experimental
unit.

``` text
image/acquisition
      ↓
classical R / R torch / optional external backends
      ↓
pheno_prediction / pheno_detection / pheno_instances
      ↓
object and plant phenotypes
      ↓
biological + experimental identity
      ↓
pheno_series
      ↓
calendar time / event-relative time / thermal time
      ↓
growth / disease / senescence / stress / functional trajectories
      ↓
repeated-measures inference + dynamic trait tables
```

## Dependency boundary

The core package remains installable without Python, CUDA, PlantCV,
YOLO, LeafMachine2, `nlme`, `lme4`, `emmeans`, `mgcv`, or `changepoint`.
Optional statistical or vision engines are invoked only when the user
requests a capability that requires them.

No package-load hook installs external dependencies, downloads model
weights, creates Python environments, or accesses the network.

## Canonical vision objects retained from 0.1.0–0.3.0

- `pheno_prediction`: prediction, class probabilities, uncertainty,
  model and engine provenance.
- `pheno_detection`: object class, confidence, bounding box, coordinate
  system and model provenance.
- `pheno_instances`: per-object masks plus class/confidence/object
  metadata.

These objects remain compatible with morphology, disease, texture,
counting, aggregation and spatial linkage.

## New longitudinal object

`pheno_series` stores a long-form phenotype table plus explicit mappings
for:

- biological/experimental subject;
- time;
- trait;
- numeric value;
- optional groups such as environment, treatment and block;
- unit/source/quality metadata.

The underlying data frame is preserved so that the object remains
transparent and compatible with ordinary R workflows.

## Temporal identity and clocks

Version 0.4.0 distinguishes:

- acquisition/calendar time;
- days after an origin or event;
- thermal time;
- aligned/interpolated grid time.

Derived clocks are added rather than silently replacing original
acquisition time. Interpolation is explicit and does not create
biological replication.

## Growth and dynamic traits

The growth layer provides common parametric and flexible models and
derives interpretable quantities such as AUC, maximum fitted response,
maximum rate and threshold times. Interval AGR/RGR remain separate from
model-based derivatives.

Model family, smoothing, time scale and derived-trait definitions are
part of phenotype provenance.

## Disease, senescence, phenology and events

Image-derived severity can be converted to disease-progress summaries
while retaining the original severity series. Senescence, phenological
thresholds, changepoints, event responses, stress/recovery and
derivative summaries use explicit operational definitions rather than
implicit biological claims.

## Repeated measures

Repeated observations from one plant/plot are dependent. The 0.4.0 API
therefore exposes subject identity and residual-correlation assumptions.
A transparent `lm` fallback is available, while `nlme`, `lme4` and
`emmeans` are optional engines.

Resampling defaults to whole biological subjects, not individual time
points.

## Functional trajectories

[`pheno_functional()`](https://wep69.github.io/OmniPhenoR/reference/pheno_functional.md)
constructs a common-grid trajectory matrix.
[`pheno_fpca()`](https://wep69.github.io/OmniPhenoR/reference/pheno_fpca.md)
and
[`pheno_trajectory_cluster()`](https://wep69.github.io/OmniPhenoR/reference/pheno_trajectory_cluster.md)
summarize whole-curve variation. Interpolation grid and component
interpretation remain visible scientific choices.

## Environmental integration

Weather/environmental records can be joined by environment and
acquisition time or summarized over retrospective windows. Temporal
direction is explicit so future environmental information is not
silently introduced into earlier phenotype records.

## Precompiled vignette architecture

Source Rmd files remain in `vignettes/`. Precompiled HTML is distributed
in `inst/doc/`. Compact demonstration outputs are stored in
`inst/extdata/vignette-results/`, with hashes/provenance in
`inst/metadata/`. Rebuild scripts remain in `data-raw/vignette-results/`
and are excluded from ordinary package builds where appropriate.

Normal GitHub installation can use `build_vignettes = FALSE`. Final
release validation must nevertheless rebuild all vignette sources with
R/knitr and verify the exact tarball before freeze.

## Implementation registry

The 0.4.0 registry contains 180 blocks:

- blocks 1–32: 0.1.0 classical core;
- blocks 33–72: 0.2.0 model/deep-learning/uncertainty/tiling extensions;
- blocks 73–122: 0.3.0
  Python/backend/detection/instance/format/identity/reproducibility
  extensions;
- blocks 123–180: 0.4.0 longitudinal identity, thermal time, growth,
  disease, repeated measures, functional data, environment, events and
  temporal validation.

Use
[`pheno_blocks()`](https://wep69.github.io/OmniPhenoR/reference/pheno_blocks.md)
to inspect the machine-readable registry.

# OmniPhenoR 1.0 consolidated architecture

``` text
RAW ACQUISITION
      |
      v
CLASSICAL IMAGE CORE (0.1)
      |
      +----------------------+
      |                      |
      v                      v
R-NATIVE DL (0.2)      EXTERNAL CV (0.3)
      |                      |
      +-----------+----------+
                  |
                  v
       CANONICAL VISION OBJECTS
                  |
                  v
      LONGITUDINAL CORE (0.4)
                  |
                  v
 EXPERIMENT / DATA INFRASTRUCTURE (0.5)
                  |
        +---------+---------+
        |         |         |
      MIAPPE    BrAPI      FAIR
        |         |         |
        +---------+---------+
                  |
                  v
       REPRODUCIBILITY BUNDLE
```

The 1.0 registry contains 240 implementation blocks. The release
deliberately preserves the richer 0.4 longitudinal API where 0.5
introduced incompatible development-time simplifications, while
retaining 0.5 experiment infrastructure and all non-conflicting
convenience entry points.

The core installation remains offline-first. Python, GPU, Arrow, DuckDB,
network services, and mixed-model engines are optional capabilities.
