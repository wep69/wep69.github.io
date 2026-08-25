# OmniPhenoR

[![Version](https://img.shields.io/badge/version-1.0.0-blue)](https://wep69.github.io/OmniPhenoR/)
[![R CMD
check](https://img.shields.io/badge/R_CMD_check_--as--cran-Status_OK-brightgreen)](https://wep69.github.io/OmniPhenoR/)
[![License](https://img.shields.io/badge/license-MIT-informational)](https://wep69.github.io/OmniPhenoR/)

## OmniPhenoR 1.0.0 — Unified Multiscale Plant Digital Phenotyping Workflows

OmniPhenoR 1.0.0 consolidates the validated 0.1.0–0.5.0 development
series into one stable R-first architecture for auditable plant digital
phenotyping: classical image analysis, native R `torch` deep learning,
optional external computer-vision backends, longitudinal phenotyping
from repeated acquisitions to dynamic agronomic traits, experiment-level
data infrastructure, MIAPPE/BrAPI interoperability, FAIR evidence
auditing, and portable reproducibility bundles.

The public API preserves the union of all exports observed across the
five development generations: **223 exported functions**, **20 S3
methods**, a **240-block** implementation registry (32/40/50/58/60 per
generation) and **44 precompiled vignettes**.

## Installation

``` r

# Install pak if not already installed
install.packages("pak", repos = "https://r-lib.github.io/p/pak/dev/")

# Install OmniPhenoR (fast, no vignettes)
pak::pak("wep69/OmniPhenoR")
```

All 44 vignettes ship **precompiled** inside the package, so they remain
fully available even with this fast installation:

``` r

library(OmniPhenoR)
vignette(package = "OmniPhenoR")
vignette("v43-omniphenor-1.0-integrated-workflow", package = "OmniPhenoR")
```

Alternative with source rebuild of vignettes (slower;
maintainer/reproducibility use):

``` r

remotes::install_github("wep69/OmniPhenoR", build_vignettes = TRUE)
```

Optional capabilities (`torch`, PlantCV, Ultralytics YOLO, LeafMachine2,
`nlme`, `lme4`, `emmeans`, `mgcv`, Arrow/DuckDB, Python via
`reticulate`) are never required for installation, documentation access,
or the core core workflows.

## From an image to a longitudinal phenotype

``` r

img   <- pheno_data("leaf_rgb")
mask  <- pheno_segment(img, index = "ExG")
shape <- pheno_morphology(mask)
```

Repeated plant-level traits are represented explicitly:

``` r

d <- pheno_data("growth_series")
s <- pheno_series(
  d,
  id    = "plant_id",
  time  = "day",
  trait = "trait",
  value = "value",
  group = c("treatment", "block", "environment")
)

pheno_series_validate(s)
```

Dynamic traits are derived without losing the original trajectory:

``` r

leaf <- subset(d, trait == "leaf_area")
fit  <- pheno_growth(
  subset(leaf, plant_id == "P01"),
  "day", "value", model = "spline"
)
pheno_growth_traits(fit)

pheno_tidy(leaf, "plant_id", "day", "value")
```

Repeated-measures inference retains subject identity:

``` r

m <- pheno_repeated(
  leaf,
  response  = "value",
  time      = "day",
  treatment = "treatment",
  subject   = "plant_id",
  block     = "block",
  engine    = "lm"
)
pheno_model_diagnostics(m)$summary
```

## Documentation

- Online documentation: <https://wep69.github.io/OmniPhenoR/>
- `v00` — foundations-to-advanced tutorial (long form)
- `v01`–`v12` — classical image analysis and native R deep learning
- `v13`–`v22` — optional Python/external backends, detection and
  interoperability
- `v23`–`v32` — longitudinal phenotyping, growth, thermal time, repeated
  measures, functional data, disease progress, stress/recovery, UAV time
  series, environmental covariates, temporal validation
- `v33`–`v42` — experiment data model, catalogs/storage,
  Parquet-Arrow-DuckDB, MIAPPE, BrAPI, FAIR,
  provenance/checksums/lineage, controlled vocabularies, portable
  research packages, publication and reuse
- `v43` — integrated end-to-end 1.0 workflow

All vignettes remain installed and available even when GitHub
installation uses `build_vignettes = FALSE`.

## Validation status (frozen release)

The exact released tarball passed the complete local validation protocol
on 2026-08-24 with R 4.6.0 (Windows ucrt): testthat FAIL 0 / SKIP 0 /
PASS 388; twenty master numeric controls; physical image-fixture
verification (SHA-256); 44/44 vignette renders; `R CMD check --as-cran`
**Status OK**; installation of the exact tarball in an isolated library
followed by smoke tests on version, exports, blocks, vignette
availability, scale truth and YOLO round-trip.

Frozen artifact:

| Field | Value |
|----|----|
| File | `OmniPhenoR_1.0.0.tar.gz` |
| SHA-256 | `19f19ebbe353d6cab20430534d13058a762504531a2900879fb9bf166af89172` |

See
[`dev-docs/FREEZE_1.0.0.md`](https://wep69.github.io/OmniPhenoR/dev-docs/FREEZE_1.0.0.md),
[NEWS.md](https://wep69.github.io/OmniPhenoR/NEWS.md),
[`dev-docs/MIGRATION_TO_1.0.md`](https://wep69.github.io/OmniPhenoR/dev-docs/MIGRATION_TO_1.0.md)
and
[`dev-docs/INTEGRATED_VALIDATION_0.1_TO_1.0.md`](https://wep69.github.io/OmniPhenoR/dev-docs/INTEGRATED_VALIDATION_0.1_TO_1.0.md)
for details.
