# OmniPhenoR RGB Indices: Definitions, Scaling, Selection, and Validation

## 1. Purpose

This focused vignette develops the RGB layer introduced in the long
tutorial. The goal is not to encourage calculation of every available
index. It shows how to treat visible-band indices as versioned
mathematical definitions, how to handle 0-1 and 0-255 encodings, how to
compare candidate indices against an explicit region of interest, and
how to prevent index selection from leaking downstream biological
inference into image preprocessing.

Historical work established normalized chromatic coordinates and
excess-green approaches for plant/background discrimination ([Woebbecke
et al. 1995](#ref-Woebbecke1995)). Visible normalized-difference
approaches and VARI were developed for vegetation-fraction estimation
([Gitelson et al. 2002](#ref-Gitelson2002)), while TGI provides a
visible-band chlorophyll-oriented formulation ([Hunt et al.
2013](#ref-Hunt2013)). Agricultural image-analysis studies added further
transforms and segmentation strategies ([Guijarro et al.
2011](#ref-Guijarro2011); [Meyer et al. 2004](#ref-Meyer2004)).

## 2. Inspect the registry

``` r

cat <- pheno_rgb_catalog()
cat
#> # A tibble: 59 × 7
#>    index family formula                 scaling canonical alias_of reference_key
#>    <chr> <chr>  <chr>                   <chr>   <lgl>     <chr>    <chr>        
#>  1 R     basic  R                       native  TRUE      NA       NA           
#>  2 G     basic  G                       native  TRUE      NA       NA           
#>  3 B     basic  B                       native  TRUE      NA       NA           
#>  4 r     basic  R/(R+G+B)               invari… TRUE      NA       NA           
#>  5 g     basic  G/(R+G+B)               invari… TRUE      NA       NA           
#>  6 b     basic  B/(R+G+B)               invari… TRUE      NA       NA           
#>  7 INT   basic  (R+G+B)/3               native  TRUE      NA       NA           
#>  8 LUMA  basic  0.2126R+0.7152G+0.0722B unit    TRUE      NA       NA           
#>  9 Grey  basic  0.2898r+0.5870g+0.1140b invari… TRUE      NA       NA           
#> 10 BI    basic  sqrt((R^2+G^2+B^2)/3)   native  TRUE      NA       NA           
#> # ℹ 49 more rows
as.data.frame(table(cat$family))
#>              Var1 Freq
#> 1           basic   13
#> 2 color-composite    8
#> 3      difference    6
#> 4           ratio    6
#> 5      vegetation   26
```

Version 0.1.0 contains 59 named descriptors spanning basic color
coordinates, channel differences, ratios, vegetation-oriented
transforms, and composites. The catalog exposes formula text, scaling
behavior, canonical/alias status, and reference keys.

## 3. Teaching image and QC

``` r

img <- pheno_data("leaf_rgb")
leaf <- pheno_data("leaf_mask")
pheno_qc(img)
#> # A tibble: 1 × 12
#>   height width finite_fraction missing_fraction    min   max dynamic_range  mean
#>    <int> <int>           <dbl>            <dbl>  <dbl> <dbl>         <dbl> <dbl>
#> 1     96   128               1                0 0.0769  0.86         0.783 0.649
#> # ℹ 4 more variables: sd <dbl>, low_saturation_fraction <dbl>,
#> #   high_saturation_fraction <dbl>, gradient_energy <dbl>
```

The image is synthetic and deterministic. It is useful for algorithm
understanding and exact validation, not biological inference.

## 4. Canonical NGRDI versus NGRDI3

OmniPhenoR defines

``` math
NGRDI = \frac{G-R}{G+R}
```

and reserves

``` math
NGRDI3 = \frac{G-R}{R+G+B}
```

for the three-channel denominator variant. `GRVI` is retained as an
alias of the two-channel definition.

``` r

p <- data.frame(R=.20,G=.60,B=.10)
pheno_rgb_indices(p,c("NGRDI","GRVI","NGRDI3"))
#> # A tibble: 1 × 3
#>   NGRDI  GRVI NGRDI3
#>   <dbl> <dbl>  <dbl>
#> 1   0.5   0.5  0.444
```

The formula, not only the acronym, should be reported in a manuscript.

## 5. Normalized chromatic coordinates

``` r

z <- pheno_rgb_indices(img,c("r","g","b"))
summary(as.vector(z[, , "r"] + z[, , "g"] + z[, , "b"]))
#>    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
#>       1       1       1       1       1       1
```

Normalized chromatic coordinates reduce sensitivity to overall
intensity, but they do not eliminate clipping, white balance, camera
spectral-response differences, or illumination geometry.

## 6. Core vegetation-oriented indices

``` r

pheno_rgb_summary(
  img,
  c("ExG","NGRDI","GLI","VARI","RGBVI","MGRVI","TGI"),
  mask=leaf
)
#> # A tibble: 7 × 7
#>   index     n   mean     sd median     q05    q95
#>   <chr> <int>  <dbl>  <dbl>  <dbl>   <dbl>  <dbl>
#> 1 ExG    2872  0.781  0.306  0.873 -0.0440  1.03 
#> 2 NGRDI  2872  0.435  0.257  0.513 -0.266   0.634
#> 3 GLI    2872  0.478  0.187  0.538 -0.0333  0.615
#> 4 VARI   2872  0.554  0.317  0.650 -0.313   0.799
#> 5 RGBVI  2872  0.761  0.214  0.835  0.167   0.893
#> 6 MGRVI  2872  0.668  0.416  0.812 -0.497   0.904
#> 7 TGI    2872 34.6   11.5   39.1    2.85   39.6
```

`ExG = 2g-r-b` emphasizes green chromaticity ([Woebbecke et al.
1995](#ref-Woebbecke1995)). `NGRDI` expresses green-red dominance on a
relative scale. `GLI` compares doubled green with both non-green
channels. `VARI` was developed for visible vegetation-fraction work
([Gitelson et al. 2002](#ref-Gitelson2002)). `RGBVI` and `MGRVI` use
squared-channel contrasts. TGI has a visible-band chlorophyll-oriented
basis ([Hunt et al. 2013](#ref-Hunt2013)), but a camera-derived TGI is
not automatically a calibrated chlorophyll concentration.

## 7. Difference indices

``` r

pix <- data.frame(R=c(40,80,120),G=c(120,95,70),B=c(30,60,100))
pheno_rgb_indices(pix,c("GRD","RGD","GBD","RBD","BGD","BRD"),scale="byte")
#> # A tibble: 3 × 6
#>     GRD   RGD   GBD   RBD   BGD   BRD
#>   <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
#> 1    80   -80    90    10   -90   -10
#> 2    15   -15    35    20   -35   -20
#> 3   -50    50   -30    20    30   -20
```

Differences preserve a simple sign interpretation but depend on channel
magnitude and therefore on exposure and illumination.

## 8. Ratio indices and denominator safety

``` r

pheno_rgb_indices(pix,c("GRRI","GBRI","RBRI","RGRI","BGRI","BRRI"),scale="byte")
#> # A tibble: 3 × 6
#>    GRRI  GBRI  RBRI  RGRI  BGRI  BRRI
#>   <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>
#> 1 3      4     1.33 0.333 0.25  0.75 
#> 2 1.19   1.58  1.33 0.842 0.632 0.75 
#> 3 0.583  0.7   1.2  1.71  1.43  0.833
pheno_rgb_indices(data.frame(R=0,G=.5,B=.1),c("GRRI","RGRI"))
#> # A tibble: 1 × 2
#>    GRRI  RGRI
#>   <dbl> <dbl>
#> 1    NA     0
```

Near-zero denominators are converted to `NA` rather than allowing
infinite values to propagate silently.

## 9. Scale-aware historical formulas

``` r

a <- pheno_rgb_indices(data.frame(R=.2,G=.6,B=.1),c("CIVE","TGI","MxEG"),scale="unit")
b <- pheno_rgb_indices(data.frame(R=51,G=153,B=25.5),c("CIVE","TGI","MxEG"),scale="byte")
rbind(unit=a,byte=b)
#> # A tibble: 2 × 3
#>    CIVE   TGI  MxEG
#> * <dbl> <dbl> <dbl>
#> 1 -73.0    44  140.
#> 2 -73.0    44  140.
```

Some formulas include constants that imply a particular numeric scale.
OmniPhenoR makes this handling explicit.

## 10. Verify invariance where it should hold

``` r

a <- pheno_rgb_indices(data.frame(R=.2,G=.6,B=.1),c("ExG","NGRDI","GLI","VARI","RGBVI"))
b <- pheno_rgb_indices(data.frame(R=51,G=153,B=25.5),c("ExG","NGRDI","GLI","VARI","RGBVI"),scale="byte")
rbind(unit=a,byte=b)
#> # A tibble: 2 × 5
#>     ExG NGRDI   GLI  VARI RGBVI
#> * <dbl> <dbl> <dbl> <dbl> <dbl>
#> 1     1   0.5   0.6 0.571 0.895
#> 2     1   0.5   0.6 0.571 0.895
pheno_validate("rgb")
#> # A tibble: 3 × 6
#>   domain check                  estimate target tolerance pass 
#>   <chr>  <chr>                     <dbl>  <dbl>     <dbl> <lgl>
#> 1 rgb    NGRDI scale invariance      0.5    0.5     1e-12 TRUE 
#> 2 rgb    GLI scale invariance        0.6    0.6     1e-12 TRUE 
#> 3 rgb    ExG scale invariance        1      1       1e-12 TRUE
```

## 11. Foreground-background separation

``` r

inds <- c("ExG","NGRDI","GLI","VARI","RGBVI","MGRVI")
stack <- pheno_rgb_indices(img,inds)
sep <- do.call(rbind,lapply(seq_along(inds),function(k){
  v <- stack[, , k]
  data.frame(index=inds[k],leaf_mean=mean(v[leaf],na.rm=TRUE),background_mean=mean(v[!leaf],na.rm=TRUE),standardized_difference=(mean(v[leaf],na.rm=TRUE)-mean(v[!leaf],na.rm=TRUE))/stats::sd(as.vector(v),na.rm=TRUE))
}))
sep
#>   index leaf_mean background_mean standardized_difference
#> 1   ExG 0.7806343      -0.2035398                2.226466
#> 2 NGRDI 0.4345722      -0.1780822                2.131520
#> 3   GLI 0.4779301      -0.1608392                2.241146
#> 4  VARI 0.5537652      -0.3939394                2.207172
#> 5 RGBVI 0.7611410      -0.3129771                2.304032
#> 6 MGRVI 0.6678181      -0.3452164                2.139491
```

This is an image-processing diagnostic, not a treatment test. It can
help preselect segmentation candidates without using biological outcome
significance as the optimization target.

## 12. Exposure thought experiment

``` r

base <- data.frame(R=.20,G=.60,B=.10)
bright <- transform(base,R=R*1.25,G=G*1.25,B=B*1.25)
rbind(
 base=pheno_rgb_indices(base,c("GRD","ExG","NGRDI","GLI","VARI")),
 bright=pheno_rgb_indices(bright,c("GRD","ExG","NGRDI","GLI","VARI"),scale="unit")
)
#> # A tibble: 2 × 5
#>     GRD   ExG NGRDI   GLI  VARI
#> * <dbl> <dbl> <dbl> <dbl> <dbl>
#> 1   0.4     1   0.5   0.6 0.571
#> 2   0.5     1   0.5   0.6 0.571
```

Normalized formulas are invariant to a common multiplicative factor in
this idealized example. Real cameras may apply nonlinear tone curves,
white balance, sharpening, clipping, and compression.

## 13. Calibration before interpretation

``` r

cal <- pheno_calibrate_rgb(img,observed=c(.80,.76,.74),target=c(.90,.90,.90),clip=c(0,1))
rbind(
 original=pheno_rgb_summary(img,c("NGRDI","GLI","TGI"),mask=leaf),
 calibrated=pheno_rgb_summary(cal,c("NGRDI","GLI","TGI"),mask=leaf)
)
#> # A tibble: 6 × 7
#>   index     n   mean     sd median     q05    q95
#> * <chr> <int>  <dbl>  <dbl>  <dbl>   <dbl>  <dbl>
#> 1 NGRDI  2872  0.435  0.257  0.513 -0.266   0.634
#> 2 GLI    2872  0.478  0.187  0.538 -0.0333  0.615
#> 3 TGI    2872 34.6   11.5   39.1    2.85   39.6  
#> 4 NGRDI  2872  0.453  0.254  0.531 -0.242   0.649
#> 5 GLI    2872  0.484  0.183  0.543 -0.0155  0.618
#> 6 TGI    2872 41.1   13.4   46.4    4.18   47.1
```

Channel-wise gain correction is intentionally simple. If the scientific
claim depends on reflectance or cross-date comparability, use a stronger
acquisition and calibration protocol.

## 14. Avoid post-hoc index shopping

With many correlated indices, selecting the one that maximizes a
treatment difference in the same dataset can create optimistic
inference. Prefer prespecification, independent calibration images,
truth masks, repeatability studies, optical reasoning, or nested
selection for predictive tasks.

## 15. Reporting checklist

Report the exact formula, input scale, calibration method, acquisition
protocol, region of interest, summary statistic, invalid-pixel rule,
package version, legacy-name ambiguity, and validation evidence.

## 16. Minimal reproducible RGB workflow

``` r

img <- pheno_data("leaf_rgb")
leaf <- pheno_segment(img,index="ExG",threshold="otsu",min_size=50)
traits <- pheno_rgb_summary(img,c("ExG","NGRDI","GLI","VARI","TGI"),mask=leaf)
traits
#> # A tibble: 5 × 7
#>   index     n   mean     sd median     q05    q95
#>   <chr> <int>  <dbl>  <dbl>  <dbl>   <dbl>  <dbl>
#> 1 ExG    2872  0.781  0.306  0.873 -0.0440  1.03 
#> 2 NGRDI  2872  0.435  0.257  0.513 -0.266   0.634
#> 3 GLI    2872  0.478  0.187  0.538 -0.0333  0.615
#> 4 VARI   2872  0.554  0.317  0.650 -0.313   0.799
#> 5 TGI    2872 34.6   11.5   39.1    2.85   39.6
pheno_validate("rgb")
#> # A tibble: 3 × 6
#>   domain check                  estimate target tolerance pass 
#>   <chr>  <chr>                     <dbl>  <dbl>     <dbl> <lgl>
#> 1 rgb    NGRDI scale invariance      0.5    0.5     1e-12 TRUE 
#> 2 rgb    GLI scale invariance        0.6    0.6     1e-12 TRUE 
#> 3 rgb    ExG scale invariance        1      1       1e-12 TRUE
```

## References

Gitelson, Anatoly A., Yoram J. Kaufman, Robert Stark, and Don Rundquist.
2002. “Novel Algorithms for Remote Estimation of Vegetation Fraction.”
*Remote Sensing of Environment* 80 (1): 76–87.
<https://doi.org/10.1016/S0034-4257(01)00289-9>.

Guijarro, Maria, Gonzalo Pajares, I. Riomoros, P. J. Herrera, Xavier P.
Burgos-Artizzu, and Angela Ribeiro. 2011. “Automatic Segmentation of
Relevant Textures in Agricultural Images.” *Computers and Electronics in
Agriculture* 75 (1): 75–83.
<https://doi.org/10.1016/j.compag.2010.09.013>.

Hunt, E. Raymond Jr., Paul C. Doraiswamy, James E. McMurtrey, Craig S.
T. Daughtry, Eileen M. Perry, and Bakhyt Akhmedov. 2013. “A Visible Band
Index for Remote Sensing Leaf Chlorophyll Content at the Canopy Scale.”
*International Journal of Applied Earth Observation and Geoinformation*
21: 103–12. <https://doi.org/10.1016/j.jag.2012.07.020>.

Meyer, George E., Joao Camargo Neto, David D. Jones, and Timothy W.
Hindman. 2004. “Intensified Fuzzy Clusters for Classifying Plant, Soil,
and Residue Regions of Interest from Color Images.” *Computers and
Electronics in Agriculture* 42 (3): 161–80.
<https://doi.org/10.1016/j.compag.2003.08.002>.

Woebbecke, D. M., G. E. Meyer, K. Von Bargen, and D. A. Mortensen. 1995.
“Color Indices for Weed Identification Under Various Soil, Residue, and
Lighting Conditions.” *Transactions of the ASAE* 38 (1): 259–69.
<https://doi.org/10.13031/2013.27838>.
