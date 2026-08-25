# OmniPhenoR Environments, Precompiled Vignettes, and Reproducible Release Workflows

## Purpose

OmniPhenoR’s documentation is designed to remain long, illustrated, and
immediately available after installation without forcing GitHub users to
rebuild every vignette. Expensive analyses are computed deliberately,
compact outputs are frozen with provenance, and HTML is precompiled for
release.

![](assets/precompiled-vignettes.svg)

## 1. User installation versus maintainer validation

These are intentionally different workflows.

### User

``` r

remotes::install_github(
  "wep69/OmniPhenoR",
  build_vignettes = FALSE
)

vignette(package = "OmniPhenoR")
```

### Maintainer

``` text
regenerate changed heavy outputs
→ render every affected Rmd
→ inspect HTML
→ tests
→ R CMD build
→ R CMD check
→ R CMD check --as-cran
→ install exact tarball
→ verify vignette(package = ...)
→ SHA-256
```

The user does not repeat work that belongs to release preparation.

## 2. Installed vignette registry

``` r

pheno_vignettes()
#> # A tibble: 44 × 18
#>    artifact_id   vignette title source_script created_on package_version backend
#>    <chr>         <chr>    <chr> <chr>         <chr>      <chr>           <chr>  
#>  1 v00-foundati… v00-fou… Omni… data-raw/vig… 2026-08-24 1.0.0           core   
#>  2 v01-rgb-indi… v01-rgb… Omni… data-raw/vig… 2026-08-24 1.0.0           core   
#>  3 v02-texture-… v02-tex… Omni… data-raw/vig… 2026-08-24 1.0.0           core   
#>  4 v03-leaf-hea… v03-lea… Omni… data-raw/vig… 2026-08-24 1.0.0           core   
#>  5 v04-validati… v04-val… Omni… data-raw/vig… 2026-08-24 1.0.0           core   
#>  6 v05-deep-lea… v05-dee… Omni… data-raw/vig… 2026-08-24 1.0.0           core/o…
#>  7 v06-semantic… v06-sem… Omni… data-raw/vig… 2026-08-24 1.0.0           core/o…
#>  8 v07-image-cl… v07-ima… Omni… data-raw/vig… 2026-08-24 1.0.0           core/o…
#>  9 v08-training… v08-tra… Omni… data-raw/vig… 2026-08-24 1.0.0           core/o…
#> 10 v09-model-re… v09-mod… Omni… data-raw/vig… 2026-08-24 1.0.0           core/o…
#> # ℹ 34 more rows
#> # ℹ 11 more variables: backend_version <lgl>, model_id <lgl>, model_hash <lgl>,
#> #   input_hash <lgl>, seed <lgl>, source_hash <chr>, output_file <chr>,
#> #   output_hash <chr>, precompiled <lgl>, render_mode <chr>, notes <chr>
pheno_vignettes("Python|YOLO")
#> # A tibble: 4 × 18
#>   artifact_id    vignette title source_script created_on package_version backend
#>   <chr>          <chr>    <chr> <chr>         <chr>      <chr>           <chr>  
#> 1 v13-python-ba… v13-pyt… Omni… data-raw/vig… 2026-08-24 1.0.0           core/o…
#> 2 v17-yolo-work… v17-yol… Omni… data-raw/vig… 2026-08-24 1.0.0           core/o…
#> 3 v19-coco-and-… v19-coc… Omni… data-raw/vig… 2026-08-24 1.0.0           core/o…
#> 4 v22-python-en… v22-pyt… Omni… data-raw/vig… 2026-08-24 1.0.0           core/o…
#> # ℹ 11 more variables: backend_version <lgl>, model_id <lgl>, model_hash <lgl>,
#> #   input_hash <lgl>, seed <lgl>, source_hash <chr>, output_file <chr>,
#> #   output_hash <chr>, precompiled <lgl>, render_mode <chr>, notes <chr>
```

The registry is stored in `inst/metadata/vignette_artifacts.csv` and
records source/output hashes plus backend notes.

## 3. Frozen compact results

``` r

pheno_example_result("mock_yolo_leaf_detection")
#>    image_id object_id  class confidence xmin ymin xmax ymax    engine
#> 1 plant_001         1   leaf       0.96   18   14   72   93 mock_yolo
#> 2 plant_001         2   leaf       0.89   78   19  126   98 mock_yolo
#> 3 plant_001         3 flower       0.84   52    8   70   27 mock_yolo
#>                model_id
#> 1 frozen_teaching_model
#> 2 frozen_teaching_model
#> 3 frozen_teaching_model
pheno_example_result("cross_engine_demo")
#>            engine dice  iou area_bias runtime_sec                   note
#> 1      native_exg 0.94 0.89    -0.012       0.018 frozen teaching result
#> 2      torch_unet 0.97 0.94    -0.004       0.052 frozen teaching result
#> 3 plantcv_adapter 0.95 0.91     0.008       0.074  frozen adapter result
```

These are intentionally small. Large raw datasets and third-party model
weights do not belong inside the R package.

## 4. Heavy computations remain reproducible

The source repository includes `data-raw/vignette-results/` scripts.
These scripts are excluded from the ordinary CRAN build but remain in
the development repository. A developer can deliberately regenerate
frozen examples when a backend or scientific method changes.

Every expensive result should record:

``` text
artifact id
source script
package version
R version
Python version
backend version
model id + SHA-256
input hash
seed
important arguments
output hash
```

## 5. Why not rely on knitr cache?

Knitr cache is useful during development but is a poor release
provenance mechanism. It can be invalidated by package changes, depend
on local paths, become large, and obscure which scientific result was
actually frozen. Explicit result artifacts and hashes are easier to
audit.

## 6. Offline documentation

The installed HTML must not require an internet connection merely to
display key figures. Documentation assets are either embedded or shipped
locally. Optional backend documentation remains readable even if Python,
PlantCV, YOLO, LeafMachine2, or CUDA are absent.

## 7. Environment reports

``` r

env <- pheno_environment_report()
env$R
#> [1] "R version 4.6.0 (2026-04-24 ucrt)"
env$OmniPhenoR
#> [1] "1.0.0"
```

A real backend run can produce a Markdown environment report with Python
initialization enabled deliberately:

``` r

pheno_environment_report(
  "environment.md",
  initialize_python = TRUE
)
```

## 8. Model files remain external

``` r

pheno_pretrained_models()
#> # A tibble: 4 × 7
#>   model_id               task            backend bundled available license note 
#>   <chr>                  <chr>           <chr>   <lgl>   <lgl>     <chr>   <chr>
#> 1 user_yolo_detection    object_detecti… ultral… FALSE   FALSE     AGPL-3… No w…
#> 2 user_yolo_segmentation instance_segme… ultral… FALSE   FALSE     AGPL-3… No w…
#> 3 leafmachine2_models    herbarium_phen… leafma… FALSE   FALSE     GPL-3.… Use …
#> 4 external_maskrcnn      instance_segme… maskrc… FALSE   FALSE     model-… Regi…
pheno_model_cache(create=FALSE)
#> [1] "C:/Users/wep69/AppData/Local/R/cache/R/OmniPhenoR/models"
```

The registry intentionally reports that third-party weights are not
bundled. A download helper exists only for explicit user-driven
workflows and supports SHA-256 verification.

``` r

pheno_model_download(
  url = model_url,
  destfile = file.path(pheno_model_cache(create=TRUE), "model.bin"),
  sha256 = expected_hash,
  license = license_notice,
  accept_license = TRUE
)
```

No package vignette calls this function during ordinary rendering.

## 9. Release integrity

A precompiled HTML file is not authoritative by itself. The source Rmd
remains the scientific source document. Before a release is frozen, the
maintainer must demonstrate that the Rmd rebuilds correctly and that the
installed HTML corresponds to the release source.

If an included package source changes after hashing, the previous
tarball hash is invalid.

## 10. Historical versions

The same distribution architecture is retrofitted to the 0.1.0 and 0.2.0
source snapshots:

- source Rmd remains under `vignettes/`;
- precompiled HTML is available under `inst/doc/`;
- a vignette artifact ledger records hashes;
- GitHub installation can use `build_vignettes = FALSE`;
- local release validation can still rebuild everything deliberately.

This retrofit does not imply that the original frozen tarball hash is
unchanged. A documentation retrofit is a new source snapshot and must be
validated separately if distributed as a release.

## 11. Validation checklist

Before release:

- all Rmd sources exist;
- all expected HTML outputs exist;
- source and output hashes are recorded;
- no heavy analysis runs during normal vignette build;
- no network download occurs during examples/checks;
- vignettes open from the installed exact tarball;
- Python-free core checks pass;
- optional backend checks are documented separately;
- the final tarball receives a SHA-256 only after all checks.

## 12. Common mistakes

- Committing stale HTML after changing the Rmd.
- Using precompiled HTML as an excuse not to test vignette rebuilding.
- Including gigabytes of model weights in `inst/extdata`.
- Making a vignette initialize Python simply to report that Python is
  optional.
- Allowing a GitHub install to re-run hours of model training.
- Publishing a new hash without rechecking the exact source tarball.

## Final perspective

Precompiled vignettes allow scientific documentation to grow in depth
without making package installation slower or more fragile. The
essential discipline is to separate **release preparation** from
**ordinary use** while preserving every script, parameter, hash, and
provenance record needed for deliberate reproduction.
