# OmniPhenoR Validation, Reproducibility, and Audit Trails

## 1. Purpose

Digital phenotyping is a measurement process. Reproducibility therefore
requires more than saving final trait tables. This vignette describes
the validation hierarchy used in OmniPhenoR 0.1.0, how to audit
capabilities and workflow settings, how to hash source files, how to
separate software correctness from biological validity, and how to
freeze a release only after the exact source archive has passed the
intended checks.

## 2. Four distinct questions

A phenotyping workflow should distinguish numerical correctness,
image-processing validity, measurement validity, and scientific
validity. Passing one layer does not prove the next.

## 3. Inspect capabilities

``` r

pheno_capabilities()
#> # A tibble: 40 × 5
#>    capability     engine            package     available status   
#>    <chr>          <chr>             <chr>       <lgl>     <chr>    
#>  1 rgb_indices    native            NA          TRUE      available
#>  2 segmentation   native/torch      NA          TRUE      available
#>  3 morphology     native            NA          TRUE      available
#>  4 texture        native            NA          TRUE      available
#>  5 plant_image    pliman            pliman      TRUE      available
#>  6 orthomosaic    FIELDimageR       FIELDimageR TRUE      available
#>  7 spatial_vector sf/terra          terra       TRUE      available
#>  8 gabor          native/OpenImageR OpenImageR  TRUE      available
#>  9 wavelet        native/waveslim   waveslim    TRUE      available
#> 10 deep_learning  torch             torch       TRUE      available
#> # ℹ 30 more rows
pheno_engines()
#> # A tibble: 40 × 5
#>    capability     engine            package     available status   
#>    <chr>          <chr>             <chr>       <lgl>     <chr>    
#>  1 rgb_indices    native            NA          TRUE      available
#>  2 segmentation   native/torch      NA          TRUE      available
#>  3 morphology     native            NA          TRUE      available
#>  4 texture        native            NA          TRUE      available
#>  5 plant_image    pliman            pliman      TRUE      available
#>  6 orthomosaic    FIELDimageR       FIELDimageR TRUE      available
#>  7 spatial_vector sf/terra          terra       TRUE      available
#>  8 gabor          native/OpenImageR OpenImageR  TRUE      available
#>  9 wavelet        native/waveslim   waveslim    TRUE      available
#> 10 deep_learning  torch             torch       TRUE      available
#> # ℹ 30 more rows
```

An installed optional package is reported as available. Availability is
not the same as validated scientific performance.

## 4. Inspect implementation blocks

``` r

pheno_blocks()
#> # A tibble: 240 × 5
#>    block module              primary_function      introduced status     
#>    <int> <chr>               <chr>                 <chr>      <chr>      
#>  1     1 Project object      pheno_project         0.1.0      implemented
#>  2     2 Capability registry pheno_capabilities    0.1.0      implemented
#>  3     3 Teaching data       pheno_data            0.1.0      implemented
#>  4     4 Image ingestion     pheno_read            0.1.0      implemented
#>  5     5 Scale calibration   pheno_calibrate_scale 0.1.0      implemented
#>  6     6 RGB calibration     pheno_calibrate_rgb   0.1.0      implemented
#>  7     7 RGB index catalog   pheno_rgb_catalog     0.1.0      implemented
#>  8     8 RGB index engine    pheno_rgb_indices     0.1.0      implemented
#>  9     9 Index segmentation  pheno_segment         0.1.0      implemented
#> 10    10 Otsu segmentation   pheno_segment         0.1.0      implemented
#> # ℹ 230 more rows
```

The registry lets a release state which modules are actually
implemented. A planned feature should not be represented as available
merely because an architecture document mentions it.

## 5. Frozen numerical validation

``` r

v <- pheno_validate("all")
v
#> # A tibble: 21 × 6
#>    domain     check                      estimate target tolerance pass 
#>    <chr>      <chr>                         <dbl>  <dbl>     <dbl> <lgl>
#>  1 rgb        NGRDI scale invariance         0.5     0.5     1e-12 TRUE 
#>  2 rgb        GLI scale invariance           0.6     0.6     1e-12 TRUE 
#>  3 rgb        ExG scale invariance           1       1       1e-12 TRUE 
#>  4 morphology square area                  100     100       0     TRUE 
#>  5 morphology square perimeter              40      40       0     TRUE 
#>  6 morphology frozen rectangle area       1200    1200       0     TRUE 
#>  7 disease    known 20 percent severity     20      20       0     TRUE 
#>  8 disease    frozen severity 5 percent      5.00    5       5e- 2 TRUE 
#>  9 disease    frozen severity 10 percent    10.0    10       5e- 2 TRUE 
#> 10 disease    frozen severity 25 percent    25.0    25       5e- 2 TRUE 
#> # ℹ 11 more rows
```

The checks include exact RGB scaling identities, known geometry, known
disease severity, and constant-image texture behavior.

## 6. Unit tests complement user-facing validation

`tests/testthat/` contains regression tests for formula definitions,
spatial dimensions, connected components, scale propagation,
segmentation, disease constraints, checkerboard GLCM contrast,
directional GLRLM behavior, multiscale texture outputs, spatial joins,
spatial extraction, workflow settings, and the user-facing validation
suite.

Unit tests verify software contracts. They do not establish that a
segmentation method generalizes to a new crop, camera, disease, or field
condition.

## 7. Exact geometry validation

``` r

m <- matrix(FALSE,20,20)
m[6:15,6:15] <- TRUE
pheno_morphology(m,connectivity=4)[,c("area_px","perimeter_px")]
#> # A tibble: 1 × 2
#>   area_px perimeter_px
#>     <int>        <int>
#> 1     100           40
```

Expected values are exactly 100 pixels of area and 40 pixel-edge units
of perimeter.

## 8. Exact disease validation

``` r

leaf <- matrix(TRUE,10,10)
lesion <- matrix(FALSE,10,10)
lesion[1:2,] <- TRUE
pheno_disease(leaf_mask=leaf,lesion_mask=lesion)$summary
#> # A tibble: 1 × 4
#>   leaf_area_px lesion_area_px healthy_area_px severity_percent
#>          <int>          <int>           <int>            <dbl>
#> 1          100             20              80               20
```

Expected severity is exactly 20%.

## 9. RGB encoding validation

``` r

a <- pheno_rgb_indices(data.frame(R=.2,G=.6,B=.1),c("ExG","NGRDI","GLI","VARI","RGBVI"))
b <- pheno_rgb_indices(data.frame(R=51,G=153,B=25.5),c("ExG","NGRDI","GLI","VARI","RGBVI"),scale="byte")
rbind(unit=a,byte=b)
#> # A tibble: 2 × 5
#>     ExG NGRDI   GLI  VARI RGBVI
#> * <dbl> <dbl> <dbl> <dbl> <dbl>
#> 1     1   0.5   0.6 0.571 0.895
#> 2     1   0.5   0.6 0.571 0.895
```

Equivalent encodings should agree for scale-invariant formulas.

## 10. Texture sanity checks

``` r

constant <- matrix(1,16,16)
pheno_first_order(constant)
#> # A tibble: 1 × 18
#>       n   min   q05   q25 median   q75   q95   max  mean    sd variance    cv
#>   <int> <dbl> <dbl> <dbl>  <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>    <dbl> <dbl>
#> 1   256     1     1     1      1     1     1     1     1     0        0     0
#> # ℹ 6 more variables: skewness <dbl>, kurtosis_excess <dbl>, entropy <dbl>,
#> #   uniformity <dbl>, iqr <dbl>, mad <dbl>
pheno_fft_texture(constant)
#> # A tibble: 1 × 7
#>   spectral_entropy spectral_centroid spectral_spread low_frequency_fraction
#>              <dbl>             <dbl>           <dbl>                  <dbl>
#> 1                0                 0               0                      0
#> # ℹ 3 more variables: mid_frequency_fraction <dbl>,
#> #   high_frequency_fraction <dbl>, dominant_frequency <dbl>
```

A constant image has zero variance and no centered spatial-frequency
power.

## 11. Project-level annotation validation

For real data, create independent annotated sets. For segmentation,
report overlap metrics such as IoU and Dice. For quantitative physical
traits, report bias and error in meaningful units. Correlation alone can
be misleading when systematic bias is present.

## 12. Repeated-acquisition validation

When traits are sensitive to camera positioning, illumination, or
operator decisions, repeated acquisition can quantify repeatability.
This may be more informative than repeatedly running deterministic code
on one file.

## 13. Processing sensitivity

Consequential parameters can include segmentation threshold, minimum
object size, RGB calibration, resizing, GLCM levels, pixel distance,
direction, Gabor wavelength, wavelet level, and ROI definition.
Sensitivity analysis should vary plausible values for scientific
reasons, not search for a combination that maximizes a treatment
p-value.

## 14. Audit workflow settings

``` r

p <- pheno_pipeline(pheno_data("leaf_rgb"),"leaf_health")
a <- pheno_audit(p)
a$settings
#> $segment
#> $segment$index
#> [1] "ExG"
#> 
#> $segment$threshold
#> [1] "otsu"
#> 
#> $segment$min_size
#> [1] 50
#> 
#> 
#> $rgb
#> $rgb$indices
#> [1] "ExG"   "NGRDI" "GLI"   "VARI"  "TGI"  
#> 
#> 
#> $disease
#> $disease$lesion_index
#> [1] "ExR"
#> 
#> 
#> $texture
#> $texture$methods
#> [1] "first_order" "glcm"        "lbp"
a$R
#> [1] "R version 4.6.0 (2026-04-24 ucrt)"
a$package
#> [1] "1.0.0"
```

## 15. Hash source files

``` r

f <- tempfile(fileext=".txt")
writeLines("frozen input",f)
pheno_audit(files=f)$files
#> # A tibble: 1 × 2
#>   path                                                              sha256      
#>   <chr>                                                             <chr>       
#> 1 C:/Users/wep69/AppData/Local/Temp/RtmpIDtPzY/file54bc6ee1197b.txt 43cf9bc5d1e…
```

A SHA-256 hash identifies the exact byte sequence analyzed. Store input
manifests with hashes when feasible.

## 16. Reference metadata audit

Release references are tracked in
`inst/metadata/reference_verification.csv`. The policy requires at least
two metadata sources per scientific reference.
`inst/METADATA_VERIFICATION.md` records rules and unresolved
discrepancies.

The current bibliography covers RGB indices ([Woebbecke et al.
1995](#ref-Woebbecke1995); [Gitelson et al. 2002](#ref-Gitelson2002);
[Hunt et al. 2013](#ref-Hunt2013)), plant image integration ([Olivoto
2022](#ref-Olivoto2022)), field orthomosaics ([Matias et al.
2020](#ref-Matias2020)), and classical texture families ([Haralick et
al. 1973](#ref-Haralick1973); [Galloway 1975](#ref-Galloway1975); [Ojala
et al. 2002](#ref-Ojala2002); [Dalal and Triggs 2005](#ref-Dalal2005);
[Gabor 1946](#ref-Gabor1946); [Mallat 1989](#ref-Mallat1989); [Laws
1980](#ref-Laws1980)).

## 17. Three release-validation levels

### 17.1 Source tree

Run roxygen generation, package tests, and vignette rendering from the
source tree.

### 17.2 Built source archive

Build the `.tar.gz`, install or check from that archive, and verify that
no required file was excluded by `.Rbuildignore`.

### 17.3 CRAN-like check

Run `R CMD check --as-cran` on the exact tarball intended for
distribution. A source-tree-only check is not a substitute for archive
validation.

## 18. Local commands

``` r

setwd("D:/temp/OmniPhenoR")
roxygen2::roxygenise()
testthat::test_local()
devtools::check(document=FALSE,manual=TRUE,vignettes=TRUE)

setwd("D:/temp")
system2(file.path(R.home("bin"),"R.exe"),c("CMD","build","OmniPhenoR"))
system2(file.path(R.home("bin"),"R.exe"),c("CMD","check","--as-cran","OmniPhenoR_0.1.0.tar.gz"))
```

## 19. What counts as a real NOTE or WARNING

Environment-specific messages still require inspection. Package-caused
issues include undocumented objects, missing imports, malformed URLs or
metadata, undeclared dependencies, examples that fail, vignette
failures, invalid Rd syntax, nonportable files, or source files that
cannot reproduce the built archive.

Do not hide a check issue by deleting logs or weakening the validation
command.

## 20. Freeze criteria for 0.1.0

The release can be called frozen only when:

- source files parse;
- roxygen generation is inspected;
- testthat passes;
- all focused vignettes and the long tutorial render;
- examples execute during package check;
- `R CMD build` succeeds;
- the exact tarball passes `R CMD check --as-cran` with no
  package-caused ERROR, WARNING, or NOTE;
- citation and metadata files are internally consistent;
- version is exactly 0.1.0 across release artifacts;
- temporary logs/build artifacts are excluded;
- source-tree and tarball contents are compared;
- release checksums are recorded.

## 21. Validation is not model selection

Do not choose segmentation, texture, or calibration settings only
because they improve a downstream biological test. Validation criteria
should be tied to image truth, measurement truth, repeatability,
held-out prediction, or other independent evidence.

## 22. Manuscript reproducibility checklist

Report acquisition protocol, file preprocessing, software version, exact
formulas and parameters, experimental-unit identifiers, source-file
checksums where feasible, validation-set composition, agreement/error
metrics, processing sensitivity, random seeds, optional backend
versions, model-weight hashes when relevant, final analysis code, and a
data/code availability statement consistent with the actual archive.

## 23. Minimal audit workflow

``` r

img <- pheno_data("leaf_rgb")
p <- pheno_pipeline(img,"leaf_area")
pheno_validate("all")
#> # A tibble: 21 × 6
#>    domain     check                      estimate target tolerance pass 
#>    <chr>      <chr>                         <dbl>  <dbl>     <dbl> <lgl>
#>  1 rgb        NGRDI scale invariance         0.5     0.5     1e-12 TRUE 
#>  2 rgb        GLI scale invariance           0.6     0.6     1e-12 TRUE 
#>  3 rgb        ExG scale invariance           1       1       1e-12 TRUE 
#>  4 morphology square area                  100     100       0     TRUE 
#>  5 morphology square perimeter              40      40       0     TRUE 
#>  6 morphology frozen rectangle area       1200    1200       0     TRUE 
#>  7 disease    known 20 percent severity     20      20       0     TRUE 
#>  8 disease    frozen severity 5 percent      5.00    5       5e- 2 TRUE 
#>  9 disease    frozen severity 10 percent    10.0    10       5e- 2 TRUE 
#> 10 disease    frozen severity 25 percent    25.0    25       5e- 2 TRUE 
#> # ℹ 11 more rows
pheno_audit(p)
#> $timestamp
#> [1] "2026-08-25 01:06:10 UTC"
#> 
#> $R
#> [1] "R version 4.6.0 (2026-04-24 ucrt)"
#> 
#> $package
#> [1] "1.0.0"
#> 
#> $class
#> [1] "pheno_pipeline"
#> 
#> $settings
#> $settings$segment
#> $settings$segment$index
#> [1] "ExG"
#> 
#> $settings$segment$threshold
#> [1] "otsu"
#> 
#> $settings$segment$min_size
#> [1] 50
#> 
#> 
#> $settings$morphology
#> list()
#> 
#> 
#> $capabilities
#> # A tibble: 40 × 5
#>    capability     engine            package     available status   
#>    <chr>          <chr>             <chr>       <lgl>     <chr>    
#>  1 rgb_indices    native            NA          TRUE      available
#>  2 segmentation   native/torch      NA          TRUE      available
#>  3 morphology     native            NA          TRUE      available
#>  4 texture        native            NA          TRUE      available
#>  5 plant_image    pliman            pliman      TRUE      available
#>  6 orthomosaic    FIELDimageR       FIELDimageR TRUE      available
#>  7 spatial_vector sf/terra          terra       TRUE      available
#>  8 gabor          native/OpenImageR OpenImageR  TRUE      available
#>  9 wavelet        native/waveslim   waveslim    TRUE      available
#> 10 deep_learning  torch             torch       TRUE      available
#> # ℹ 30 more rows
#> 
#> $files
#> NULL
```

## 24. Final principle

A reproducible phenotype is not only a number. It is a number connected
to an experimental identity, source image, processing rule, software
version, validation record, and scientific interpretation boundary.

## References

Dalal, Navneet, and Bill Triggs. 2005. “Histograms of Oriented Gradients
for Human Detection.” *2005 IEEE Computer Society Conference on Computer
Vision and Pattern Recognition* 1: 886–93.
<https://doi.org/10.1109/CVPR.2005.177>.

Gabor, Dennis. 1946. “Theory of Communication. Part 1: The Analysis of
Information.” *Journal of the Institution of Electrical Engineers - Part
III: Radio and Communication Engineering* 93 (26): 429–41.
<https://doi.org/10.1049/ji-3-2.1946.0074>.

Galloway, Mary M. 1975. “Texture Analysis Using Gray Level Run Lengths.”
*Computer Graphics and Image Processing* 4 (2): 172–79.
<https://doi.org/10.1016/S0146-664X(75)80008-6>.

Gitelson, Anatoly A., Yoram J. Kaufman, Robert Stark, and Don Rundquist.
2002. “Novel Algorithms for Remote Estimation of Vegetation Fraction.”
*Remote Sensing of Environment* 80 (1): 76–87.
<https://doi.org/10.1016/S0034-4257(01)00289-9>.

Haralick, Robert M., Karthikeyan S. Shanmugam, and Its’hak Dinstein.
1973. “Textural Features for Image Classification.” *IEEE Transactions
on Systems, Man, and Cybernetics* SMC-3 (6): 610–21.
<https://doi.org/10.1109/TSMC.1973.4309314>.

Hunt, E. Raymond Jr., Paul C. Doraiswamy, James E. McMurtrey, Craig S.
T. Daughtry, Eileen M. Perry, and Bakhyt Akhmedov. 2013. “A Visible Band
Index for Remote Sensing Leaf Chlorophyll Content at the Canopy Scale.”
*International Journal of Applied Earth Observation and Geoinformation*
21: 103–12. <https://doi.org/10.1016/j.jag.2012.07.020>.

Laws, Kenneth I. 1980. “Rapid Texture Identification.” *Proceedings of
SPIE 0238, Image Processing for Missile Guidance* 238: 376–81.
<https://doi.org/10.1117/12.959169>.

Mallat, Stephane G. 1989. “A Theory for Multiresolution Signal
Decomposition: The Wavelet Representation.” *IEEE Transactions on
Pattern Analysis and Machine Intelligence* 11 (7): 674–93.
<https://doi.org/10.1109/34.192463>.

Matias, Filipe Inacio, Maria V. Caraza-Harter, and Jeffrey B. Endelman.
2020. “FIELDimageR: An r Package to Analyze Orthomosaic Images from
Agricultural Field Trials.” *The Plant Phenome Journal* 3 (1): e20005.
<https://doi.org/10.1002/ppj2.20005>.

Ojala, Timo, Matti Pietikainen, and Topi Maenpaa. 2002. “Multiresolution
Gray-Scale and Rotation Invariant Texture Classification with Local
Binary Patterns.” *IEEE Transactions on Pattern Analysis and Machine
Intelligence* 24 (7): 971–87.
<https://doi.org/10.1109/TPAMI.2002.1017623>.

Olivoto, Tiago. 2022. “Lights, Camera, Pliman! An r Package for Plant
Image Analysis.” *Methods in Ecology and Evolution* 13 (4): 789–98.
<https://doi.org/10.1111/2041-210X.13803>.

Woebbecke, D. M., G. E. Meyer, K. Von Bargen, and D. A. Mortensen. 1995.
“Color Indices for Weed Identification Under Various Soil, Residue, and
Lighting Conditions.” *Transactions of the ASAE* 38 (1): 259–69.
<https://doi.org/10.13031/2013.27838>.
