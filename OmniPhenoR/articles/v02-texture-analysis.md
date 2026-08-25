# OmniPhenoR Texture Analysis: From First-Order Statistics to Multiscale Frequency Features

## 1. Purpose

Texture is not one algorithm and not one biological variable. This
vignette develops the nine texture families implemented in OmniPhenoR
0.1.0 and shows how quantization, distance, direction, wavelength,
scale, rotation, and region of interest change the phenotype.

The package draws on classical GLCM features ([Haralick et al.
1973](#ref-Haralick1973)), gray-level run lengths ([Galloway
1975](#ref-Galloway1975)), local binary patterns ([Ojala et al.
2002](#ref-Ojala2002)), gradient orientation ([Dalal and Triggs
2005](#ref-Dalal2005)), Gabor filtering ([Gabor 1946](#ref-Gabor1946)),
multiresolution wavelets ([Mallat 1989](#ref-Mallat1989)), and Laws
texture-energy filters ([Laws 1980](#ref-Laws1980)).

## 2. Teaching image and ROI

``` r

gray <- pheno_data("leaf_gray")
mask <- pheno_data("leaf_mask")
small <- gray[25:70,35:95]
small_mask <- mask[25:70,35:95]
pheno_qc(gray)
#> # A tibble: 1 × 12
#>   height width finite_fraction missing_fraction   min   max dynamic_range  mean
#>    <int> <int>           <dbl>            <dbl> <dbl> <dbl>         <dbl> <dbl>
#> 1     96   128               1                0 0.322 0.670         0.347 0.617
#> # ℹ 4 more variables: sd <dbl>, low_saturation_fraction <dbl>,
#> #   high_saturation_fraction <dbl>, gradient_energy <dbl>
```

## 3. First-order statistics

``` r

pheno_first_order(gray)
#> # A tibble: 1 × 18
#>       n   min   q05   q25 median   q75   q95   max  mean     sd variance    cv
#>   <int> <dbl> <dbl> <dbl>  <dbl> <dbl> <dbl> <dbl> <dbl>  <dbl>    <dbl> <dbl>
#> 1 12288 0.322 0.422 0.670  0.670 0.670 0.670 0.670 0.617 0.0980  0.00960 0.159
#> # ℹ 6 more variables: skewness <dbl>, kurtosis_excess <dbl>, entropy <dbl>,
#> #   uniformity <dbl>, iqr <dbl>, mad <dbl>
pheno_first_order(gray,mask=mask,bins=32)
#> # A tibble: 1 × 18
#>       n   min   q05   q25 median   q75   q95   max  mean     sd variance    cv
#>   <int> <dbl> <dbl> <dbl>  <dbl> <dbl> <dbl> <dbl> <dbl>  <dbl>    <dbl> <dbl>
#> 1  2872 0.322 0.322 0.428  0.456 0.480 0.508 0.539 0.446 0.0521  0.00271 0.117
#> # ℹ 6 more variables: skewness <dbl>, kurtosis_excess <dbl>, entropy <dbl>,
#> #   uniformity <dbl>, iqr <dbl>, mad <dbl>
```

First-order descriptors ignore adjacency. A shuffled image therefore
retains nearly the same first-order distribution while losing spatial
organization.

``` r

set.seed(260823)
shuffled <- matrix(sample(as.vector(gray)),nrow(gray),ncol(gray))
rbind(
 original=pheno_first_order(gray)[,c("mean","sd","entropy")],
 shuffled=pheno_first_order(shuffled)[,c("mean","sd","entropy")]
)
#> # A tibble: 2 × 3
#>    mean     sd entropy
#> * <dbl>  <dbl>   <dbl>
#> 1 0.617 0.0980    1.26
#> 2 0.617 0.0980    1.26
```

## 4. GLCM/Haralick

``` r

g <- pheno_glcm(gray,levels=16,distances=c(1,2),angles=c(0,45,90,135),mask=mask)
g
#> # A tibble: 8 × 12
#>   distance angle contrast dissimilarity homogeneity    ASM energy entropy
#>      <dbl> <dbl>    <dbl>         <dbl>       <dbl>  <dbl>  <dbl>   <dbl>
#> 1        1     0     1.44         0.737       0.689 0.0537  0.232    3.19
#> 2        1    45     2.56         0.965       0.640 0.0476  0.218    3.36
#> 3        1    90     2.35         0.923       0.648 0.0490  0.221    3.32
#> 4        1   135     2.61         0.981       0.636 0.0475  0.218    3.37
#> 5        2     0     2.34         0.963       0.628 0.0461  0.215    3.36
#> 6        2    45     4.60         1.43        0.526 0.0363  0.191    3.60
#> 7        2    90     4.00         1.30        0.552 0.0395  0.199    3.52
#> 8        2   135     4.48         1.43        0.519 0.0361  0.190    3.60
#> # ℹ 4 more variables: correlation <dbl>, max_probability <dbl>, mean_i <dbl>,
#> #   variance_i <dbl>
```

A GLCM feature depends on quantization, distance, angle, symmetry, and
ROI. “GLCM contrast” is not a complete phenotype definition.

### 4.1 Quantization sensitivity

``` r

rbind(
 levels8=pheno_glcm(gray,levels=8,distances=1,angles=0,mask=mask),
 levels32=pheno_glcm(gray,levels=32,distances=1,angles=0,mask=mask)
)
#> # A tibble: 2 × 12
#>   distance angle contrast dissimilarity homogeneity    ASM energy entropy
#> *    <dbl> <dbl>    <dbl>         <dbl>       <dbl>  <dbl>  <dbl>   <dbl>
#> 1        1     0    0.461         0.373       0.822 0.142   0.377    2.25
#> 2        1     0    5.36          1.45        0.526 0.0241  0.155    4.27
#> # ℹ 4 more variables: correlation <dbl>, max_probability <dbl>, mean_i <dbl>,
#> #   variance_i <dbl>
```

### 4.2 Direction sensitivity

``` r

pheno_glcm(gray,levels=16,distances=1,angles=c(0,45,90,135),mask=mask)[,c("angle","contrast","homogeneity","correlation")]
#> # A tibble: 4 × 4
#>   angle contrast homogeneity correlation
#>   <dbl>    <dbl>       <dbl>       <dbl>
#> 1     0     1.44       0.689       0.866
#> 2    45     2.56       0.640       0.764
#> 3    90     2.35       0.648       0.783
#> 4   135     2.61       0.636       0.759
```

Averaging directions can provide rotation tolerance, but it removes
anisotropy that may reflect venation, streaking, or image orientation.

### 4.3 Known checkerboard validation

``` r

constant <- matrix(0,16,16)
checker <- outer(1:16,1:16,function(i,j)(i+j)%%2)
rbind(
 constant=pheno_glcm(constant,levels=2,angles=0),
 checker=pheno_glcm(checker,levels=2,angles=0)
)
#> # A tibble: 2 × 12
#>   distance angle contrast dissimilarity homogeneity   ASM energy entropy
#> *    <int> <dbl>    <dbl>         <dbl>       <dbl> <dbl>  <dbl>   <dbl>
#> 1        1     0        0             0         1     1    1       0    
#> 2        1     0        1             1         0.5   0.5  0.707   0.693
#> # ℹ 4 more variables: correlation <dbl>, max_probability <dbl>, mean_i <dbl>,
#> #   variance_i <dbl>
```

## 5. GLRLM

``` r

r <- pheno_glrlm(gray,levels=16,angles=c(0,45,90,135),mask=mask)
r
#> # A tibble: 4 × 9
#>   angle  runs   SRE   LRE   GLN   RLN    RP   LGRE  HGRE
#>   <dbl> <dbl> <dbl> <dbl> <dbl> <dbl> <dbl>  <dbl> <dbl>
#> 1     0  1560 0.688  7.57  293.  687. 0.543 0.0370  49.6
#> 2    45  1705 0.693  4.54  300.  768. 0.594 0.0493  48.6
#> 3    90  1704 0.689  4.69  300.  766. 0.593 0.0461  48.6
#> 4   135  1729 0.703  4.43  299.  800. 0.602 0.0497  48.7
```

Run-length features describe persistence of similar gray values along a
direction.

``` r

stripes <- matrix(rep(rep(c(0,1),4),each=16),8,16,byrow=TRUE)
pheno_glrlm(stripes,levels=2,angles=c(0,90))
#> # A tibble: 2 × 9
#>   angle  runs     SRE   LRE   GLN   RLN     RP  LGRE  HGRE
#>   <dbl> <dbl>   <dbl> <dbl> <dbl> <dbl>  <dbl> <dbl> <dbl>
#> 1     0     8 0.00391   256     4     8 0.0625 0.625   2.5
#> 2    90   128 1           1    64   128 1      0.625   2.5
```

## 6. Local binary patterns

``` r

l <- pheno_lbp(gray,mask=mask)
l$summary
#> # A tibble: 1 × 5
#>       n entropy uniformity uniform_pattern_probability dominant_code
#>   <int>   <dbl>      <dbl>                       <dbl>         <int>
#> 1  2612    4.40     0.0530                       0.713           255
head(l$histogram)
#> # A tibble: 6 × 3
#>    code count probability
#>   <int> <int>       <dbl>
#> 1     0   160     0.0613 
#> 2     1    27     0.0103 
#> 3     2    26     0.00995
#> 4     3    18     0.00689
#> 5     4    40     0.0153 
#> 6     5    10     0.00383
pheno_lbp(gray,rotation_invariant=TRUE,mask=mask)$summary
#> # A tibble: 1 × 5
#>       n entropy uniformity uniform_pattern_probability dominant_code
#>   <int>   <dbl>      <dbl>                       <dbl>         <int>
#> 1  2612    2.95     0.0826                       0.713           255
```

Version 0.1.0 uses the eight immediate neighbors at radius one. More
general `(P,R)` neighborhoods are not silently approximated.

## 7. HOG

``` r

h <- pheno_hog(gray,bins=9,signed=FALSE,mask=mask)
h$summary
#> # A tibble: 1 × 4
#>   total_gradient mean_gradient orientation_entropy dominant_orientation
#>            <dbl>         <dbl>               <dbl>                <dbl>
#> 1           528.         0.184                2.05                   90
h$histogram
#> # A tibble: 9 × 4
#>     bin angle_mid weight probability
#>   <int>     <dbl>  <dbl>       <dbl>
#> 1     1        10   29.8      0.0565
#> 2     2        30   31.6      0.0598
#> 3     3        50   61.5      0.117 
#> 4     4        70   72.6      0.138 
#> 5     5        90  140.       0.265 
#> 6     6       110   76.2      0.144 
#> 7     7       130   54.4      0.103 
#> 8     8       150   32.5      0.0615
#> 9     9       170   29.5      0.0559
```

The current implementation is a global phenotypic orientation summary,
not the full block-normalized object-detection representation of Dalal
and Triggs ([Dalal and Triggs 2005](#ref-Dalal2005)).

## 8. Gabor filter bank

``` r

gab <- pheno_gabor(small,wavelengths=c(4,8),angles=c(0,45,90,135),mask=small_mask)
gab
#> # A tibble: 8 × 7
#>   wavelength angle mean_response sd_response  energy   q90 max_response
#>        <dbl> <dbl>         <dbl>       <dbl>   <dbl> <dbl>        <dbl>
#> 1          4     0         0.309      0.421   0.272  0.652        3.57 
#> 2          4    45         0.145      0.115   0.0343 0.294        0.732
#> 3          4    90         0.324      0.671   0.555  0.855        3.74 
#> 4          4   135         0.144      0.0995  0.0305 0.289        0.593
#> 5          8     0         2.55       2.70   13.8    5.76        13.8  
#> 6          8    45         0.784      0.562   0.930  1.59         2.92 
#> 7          8    90         1.70       2.75   10.5    7.31        10.6  
#> 8          8   135         0.845      0.534   1.000  1.66         2.64
```

Gabor response is jointly dependent on orientation and spatial
wavelength. A strong response at one setting is evidence of image
structure at that scale and direction, not a generic severity score.

## 9. Haar DWT

``` r

w <- pheno_dwt(gray,levels=3)
w$summary
#> # A tibble: 9 × 6
#>   level band    energy mean_abs     sd max_abs
#>   <int> <chr>    <dbl>    <dbl>  <dbl>   <dbl>
#> 1     1 LH    0.000581  0.00624 0.0241   0.240
#> 2     1 HL    0.000287  0.00437 0.0170   0.262
#> 3     1 HH    0.000173  0.00354 0.0132   0.140
#> 4     2 LH    0.00329   0.0182  0.0574   0.371
#> 5     2 HL    0.00134   0.0124  0.0366   0.336
#> 6     2 HH    0.000647  0.00846 0.0255   0.185
#> 7     3 LH    0.0335    0.0693  0.184    0.871
#> 8     3 HL    0.0129    0.0417  0.114    0.690
#> 9     3 HH    0.00102   0.0139  0.0320   0.132
```

Wavelet detail energy is partitioned by scale and orientation ([Mallat
1989](#ref-Mallat1989)). Fine scales can capture small lesions and
noise; coarser scales can capture broader organization. The wavelet
family and level are part of the phenotype.

## 10. Laws texture energy

``` r

pheno_laws(small,mask=small_mask)
#> # A tibble: 10 × 4
#>    filter mean_abs energy     sd
#>    <chr>     <dbl>  <dbl>  <dbl>
#>  1 L5E5      1.82  8.05   2.18  
#>  2 L5S5      0.783 1.55   0.968 
#>  3 L5W5      0.758 1.51   0.966 
#>  4 L5R5      1.53  6.80   2.11  
#>  5 E5S5      0.136 0.0370 0.136 
#>  6 E5W5      0.161 0.0527 0.164 
#>  7 E5R5      0.387 0.284  0.367 
#>  8 S5W5      0.111 0.0206 0.0907
#>  9 S5R5      0.271 0.123  0.223 
#> 10 W5R5      0.330 0.172  0.251
```

Laws filters provide compact edge, spot, wave, and ripple responses
([Laws 1980](#ref-Laws1980)). The package metadata ledger documents the
known secondary page-range discrepancy for this reference.

## 11. FFT-domain texture

``` r

pheno_fft_texture(gray)
#> # A tibble: 1 × 7
#>   spectral_entropy spectral_centroid spectral_spread low_frequency_fraction
#>              <dbl>             <dbl>           <dbl>                  <dbl>
#> 1             3.51            0.0418          0.0881                  0.963
#> # ℹ 3 more variables: mid_frequency_fraction <dbl>,
#> #   high_frequency_fraction <dbl>, dominant_frequency <dbl>
```

FFT summarizes global frequency content and has no spatial localization.
It complements rather than replaces Gabor and wavelet analysis.

## 12. Unified texture API

``` r

t <- pheno_texture(
  small,
  methods=c("first_order","glcm","glrlm","lbp","hog","gabor","dwt","laws","fft"),
  mask=small_mask,
  levels=16,
  distances=c(1,2),
  angles=c(0,90),
  wavelengths=c(4,8),
  dwt_levels=2
)
t
#> <pheno_texture>
#>   methods: first_order, glcm, glrlm, lbp, hog, gabor, dwt, laws, fft
names(t$results)
#> [1] "first_order" "glcm"        "glrlm"       "lbp"         "hog"        
#> [6] "gabor"       "dwt"         "laws"        "fft"
t$settings
#> $methods
#> [1] "first_order" "glcm"        "glrlm"       "lbp"         "hog"        
#> [6] "gabor"       "dwt"         "laws"        "fft"        
#> 
#> $levels
#> [1] 16
#> 
#> $distances
#> [1] 1 2
#> 
#> $angles
#> [1]  0 90
#> 
#> $wavelengths
#> [1] 4 8
#> 
#> $dwt_levels
#> [1] 2
```

The unified object records method settings without pretending that nine
mathematically distinct families form one unqualified texture score.

## 13. Resolution and physical scale

A distance of two pixels has different biological meaning at 0.1
mm/pixel and 1 cm/pixel. If the target is lesion size, venation spacing,
or canopy organization, standardize resolution or report physical scale.

``` r

pheno_calibrate_scale(200,50,"mm")
#> $units_per_pixel
#> [1] 0.25
#> 
#> $pixels_per_unit
#> [1] 4
#> 
#> $unit
#> [1] "mm"
#> 
#> $pixel_distance
#> [1] 200
#> 
#> $known_distance
#> [1] 50
#> 
#> attr(,"class")
#> [1] "pheno_scale"
```

## 14. Orientation as signal or nuisance

Direction is meaningful only when orientation is controlled or itself
part of the question. If leaves are positioned arbitrarily,
direction-specific features may encode operator placement more strongly
than anatomy. Standardize orientation, use rotation-invariant
descriptors, average directions only after checking anisotropy, or model
orientation explicitly.

## 15. Feature multiplicity and leakage

Crossing gray levels, directions, distances, Gabor wavelengths, wavelet
levels, and ROIs can produce many correlated variables. For prediction,
perform feature screening inside resampling at the biological-unit
level. For confirmatory treatment inference, do not choose texture
settings by maximizing the same treatment difference that will later be
tested.

## 16. Texture quality control

``` r

pheno_qc(gray)
#> # A tibble: 1 × 12
#>   height width finite_fraction missing_fraction   min   max dynamic_range  mean
#>    <int> <int>           <dbl>            <dbl> <dbl> <dbl>         <dbl> <dbl>
#> 1     96   128               1                0 0.322 0.670         0.347 0.617
#> # ℹ 4 more variables: sd <dbl>, low_saturation_fraction <dbl>,
#> #   high_saturation_fraction <dbl>, gradient_energy <dbl>
```

Texture is sensitive to blur, sharpening, compression, resizing, and
noise. A texture study should document these factors more carefully than
a simple area workflow.

## 17. Frozen texture validation

``` r

pheno_validate("texture")
#> # A tibble: 3 × 6
#>   domain  check                            estimate target tolerance pass 
#>   <chr>   <chr>                               <dbl>  <dbl>     <dbl> <lgl>
#> 1 texture constant first-order variance           0      0     1e-14 TRUE 
#> 2 texture constant centered spectral power        0      0     1e-14 TRUE 
#> 3 texture directional GLRLM distinction           1      1     0     TRUE
```

Unit tests additionally cover checkerboard GLCM, directional GLRLM, and
finite output from LBP, HOG, Gabor, DWT, Laws, and FFT.

## 18. Reporting template

Report grayscale conversion, ROI, physical resolution, resizing,
quantization, distance, direction, GLCM symmetry, LBP neighborhood and
rotation handling, HOG settings, Gabor wavelength/orientation/envelope,
wavelet family and levels, Laws filter set, FFT normalization/cutoffs,
validation, sensitivity, software version, and seeds.

## 19. Minimal texture workflow

``` r

gray <- pheno_data("leaf_gray")
mask <- pheno_data("leaf_mask")
tex <- pheno_texture(gray,c("first_order","glcm","lbp","gabor","dwt"),mask=mask,levels=16,distances=c(1,2),angles=c(0,90),wavelengths=c(4,8),dwt_levels=2)
tex
#> <pheno_texture>
#>   methods: first_order, glcm, lbp, gabor, dwt
```

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

Haralick, Robert M., Karthikeyan S. Shanmugam, and Its’hak Dinstein.
1973. “Textural Features for Image Classification.” *IEEE Transactions
on Systems, Man, and Cybernetics* SMC-3 (6): 610–21.
<https://doi.org/10.1109/TSMC.1973.4309314>.

Laws, Kenneth I. 1980. “Rapid Texture Identification.” *Proceedings of
SPIE 0238, Image Processing for Missile Guidance* 238: 376–81.
<https://doi.org/10.1117/12.959169>.

Mallat, Stephane G. 1989. “A Theory for Multiresolution Signal
Decomposition: The Wavelet Representation.” *IEEE Transactions on
Pattern Analysis and Machine Intelligence* 11 (7): 674–93.
<https://doi.org/10.1109/34.192463>.

Ojala, Timo, Matti Pietikainen, and Topi Maenpaa. 2002. “Multiresolution
Gray-Scale and Rotation Invariant Texture Classification with Local
Binary Patterns.” *IEEE Transactions on Pattern Analysis and Machine
Intelligence* 24 (7): 971–87.
<https://doi.org/10.1109/TPAMI.2002.1017623>.
