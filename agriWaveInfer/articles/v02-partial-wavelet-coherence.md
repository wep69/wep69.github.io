# Partial Wavelet Coherence: Conditioning Without Calling It Causality

## Purpose

The improved formulation is motivated by complex partial wavelet
coherency (Hu and Si 2021), while recent crop-yield work illustrates the
agricultural relevance of PWC (Wang et al. 2026).

Partial wavelet coherence asks whether a time- and scale-localized
association between a response and a focal predictor remains after
accounting for one or more conditioning series. It is not, by itself, a
causal-effect estimator.

Version 0.3.0 retains two algorithms. The default `"improved"` route
uses complex smoothed spectral matrices and supports multiple controls.
The `"legacy_biwavelet"` route exists to reproduce a classical
compatibility implementation when exactly one control is supplied.

## Agronomic example

Suppose crop yield, ENSO, rainfall, and temperature are observed on a
common regular time grid. A high bivariate yield–ENSO coherence can
arise partly because ENSO and yield are both related to rainfall. The
conditional question is therefore more precise: at which times and
scales does the yield–ENSO association persist after conditioning on
rainfall and temperature?

``` r

library(agriWaveInfer)
t <- 1:160
rain <- awi_series(t, sin(2*pi*t/24), variable = "rain")
temp <- awi_series(t, cos(2*pi*t/40), variable = "temperature")
enso <- awi_series(t, 0.8*rain$value + sin(2*pi*t/12), variable = "ENSO")
yld <- awi_series(t, 0.7*rain$value + 0.4*temp$value + cos(2*pi*t/18),
                  variable = "yield")

fit <- awi_pwc(
  yld, enso,
  controls = list(rain, temp),
  condition = awi_condition_spec(method = "svd")
)
range(fit$coherence, na.rm = TRUE)
#> [1] 2.228268e-06 9.986397e-01
summary(fit$condition_number)
#>        V1                 V2                 V3                 V4          
#>  Min.   :   2.645   Min.   :   2.611   Min.   :   2.578   Min.   :   2.545  
#>  1st Qu.:  18.577   1st Qu.:  18.174   1st Qu.:  17.767   1st Qu.:  17.366  
#>  Median :  79.766   Median :  80.420   Median :  81.070   Median :  81.716  
#>  Mean   : 421.391   Mean   : 396.776   Mean   : 371.744   Mean   : 347.157  
#>  3rd Qu.: 646.419   3rd Qu.: 639.001   3rd Qu.: 630.922   3rd Qu.: 622.439  
#>  Max.   :3096.013   Max.   :2648.285   Max.   :2228.403   Max.   :1844.692  
#>        V5                 V6                 V7                V8         
#>  Min.   :   2.512   Min.   :   2.479   Min.   :  2.447   Min.   :  2.415  
#>  1st Qu.:  16.970   1st Qu.:  16.581   1st Qu.: 16.256   1st Qu.: 16.377  
#>  Median :  82.360   Median :  83.002   Median : 83.644   Median : 84.285  
#>  Mean   : 323.762   Mean   : 302.094   Mean   :282.658   Mean   :266.231  
#>  3rd Qu.: 614.011   3rd Qu.: 606.231   3rd Qu.:599.762   3rd Qu.:518.017  
#>  Max.   :1499.655   Max.   :1193.694   Max.   :951.767   Max.   :969.100  
#>        V9                V10                V11                V12          
#>  Min.   :   2.384   Min.   :   2.353   Min.   :   2.323   Min.   :   2.292  
#>  1st Qu.:  16.530   1st Qu.:  16.700   1st Qu.:  16.662   1st Qu.:  16.630  
#>  Median :  84.928   Median :  85.572   Median :  86.219   Median :  86.492  
#>  Mean   : 254.021   Mean   : 247.625   Mean   : 249.593   Mean   : 264.381  
#>  3rd Qu.: 438.033   3rd Qu.: 412.070   3rd Qu.: 380.703   3rd Qu.: 406.116  
#>  Max.   :1000.598   Max.   :1044.065   Max.   :1093.288   Max.   :1455.772  
#>       V13                V14                V15                V16          
#>  Min.   :   2.263   Min.   :   2.234   Min.   :   2.205   Min.   :   2.176  
#>  1st Qu.:  16.607   1st Qu.:  16.135   1st Qu.:  16.253   1st Qu.:  16.473  
#>  Median :  86.166   Median :  85.645   Median :  85.082   Median :  84.468  
#>  Mean   : 294.811   Mean   : 335.110   Mean   : 376.598   Mean   : 414.928  
#>  3rd Qu.: 405.999   3rd Qu.: 438.724   3rd Qu.: 445.550   3rd Qu.: 454.156  
#>  Max.   :2262.567   Max.   :2878.420   Max.   :3137.275   Max.   :3212.480  
#>       V17                V18                V19                V20          
#>  Min.   :   2.148   Min.   :   2.121   Min.   :   2.094   Min.   :   2.067  
#>  1st Qu.:  16.728   1st Qu.:  17.020   1st Qu.:  17.352   1st Qu.:  17.301  
#>  Median :  83.797   Median :  84.187   Median :  84.891   Median :  85.608  
#>  Mean   : 448.866   Mean   : 478.598   Mean   : 504.760   Mean   : 527.627  
#>  3rd Qu.: 530.752   3rd Qu.: 476.000   3rd Qu.: 492.110   3rd Qu.: 582.382  
#>  Max.   :3233.946   Max.   :3244.458   Max.   :3253.726   Max.   :3263.372  
#>       V21                V22                V23                V24          
#>  Min.   :   2.041   Min.   :   2.016   Min.   :   1.991   Min.   :   1.966  
#>  1st Qu.:  16.595   1st Qu.:  16.525   1st Qu.:  16.752   1st Qu.:  17.002  
#>  Median :  80.105   Median :  79.791   Median :  87.845   Median :  84.804  
#>  Mean   : 548.313   Mean   : 567.361   Mean   : 584.027   Mean   : 597.699  
#>  3rd Qu.: 526.448   3rd Qu.: 543.081   3rd Qu.: 630.203   3rd Qu.: 641.783  
#>  Max.   :3273.554   Max.   :3284.267   Max.   :3295.499   Max.   :3307.264  
#>       V25                V26                V27                V28          
#>  Min.   :   1.943   Min.   :   1.919   Min.   :   1.897   Min.   :   1.874  
#>  1st Qu.:  17.070   1st Qu.:  16.755   1st Qu.:  16.337   1st Qu.:  16.302  
#>  Median :  81.686   Median :  81.689   Median :  84.328   Median :  76.359  
#>  Mean   : 606.269   Mean   : 609.915   Mean   : 609.609   Mean   : 603.826  
#>  3rd Qu.: 584.933   3rd Qu.: 647.312   3rd Qu.: 634.031   3rd Qu.: 525.418  
#>  Max.   :3319.570   Max.   :3332.438   Max.   :3345.880   Max.   :3359.918  
#>       V29                V30                V31                V32          
#>  Min.   :   1.853   Min.   :   1.831   Min.   :   1.811   Min.   :   1.791  
#>  1st Qu.:  16.825   1st Qu.:  17.142   1st Qu.:  17.294   1st Qu.:  15.282  
#>  Median :  72.641   Median :  73.266   Median :  60.937   Median :  47.343  
#>  Mean   : 593.442   Mean   : 581.551   Mean   : 569.618   Mean   : 558.675  
#>  3rd Qu.: 422.927   3rd Qu.: 382.087   3rd Qu.: 290.629   3rd Qu.: 218.002  
#>  Max.   :3374.559   Max.   :3389.824   Max.   :3405.719   Max.   :3422.261  
#>       V33                V34                V35                V36          
#>  Min.   :   1.772   Min.   :   1.753   Min.   :   1.371   Min.   :   1.255  
#>  1st Qu.:  13.972   1st Qu.:  12.194   1st Qu.:   9.505   1st Qu.:   9.118  
#>  Median :  47.588   Median :  45.940   Median :  40.246   Median :  38.819  
#>  Mean   : 549.803   Mean   : 543.442   Mean   : 539.461   Mean   : 537.477  
#>  3rd Qu.: 165.938   3rd Qu.: 113.438   3rd Qu.:  99.538   3rd Qu.:  78.146  
#>  Max.   :3439.454   Max.   :3457.313   Max.   :3475.840   Max.   :3495.049  
#>       V37                V38                V39                V40          
#>  Min.   :   1.548   Min.   :   1.684   Min.   :   1.522   Min.   :   1.478  
#>  1st Qu.:   7.957   1st Qu.:   7.082   1st Qu.:   7.253   1st Qu.:   6.344  
#>  Median :  33.287   Median :  28.973   Median :  20.800   Median :  18.264  
#>  Mean   : 537.067   Mean   : 537.874   Mean   : 539.694   Mean   : 542.506  
#>  3rd Qu.:  78.609   3rd Qu.:  79.086   3rd Qu.:  80.132   3rd Qu.:  81.333  
#>  Max.   :3514.941   Max.   :3535.529   Max.   :3556.811   Max.   :3578.802  
#>       V41                V42                V43                V44          
#>  Min.   :   1.614   Min.   :   1.625   Min.   :   1.612   Min.   :   1.599  
#>  1st Qu.:   5.646   1st Qu.:   5.189   1st Qu.:   4.255   1st Qu.:   3.721  
#>  Median :  21.515   Median :  22.803   Median :  28.775   Median :  29.981  
#>  Mean   : 546.388   Mean   : 551.331   Mean   : 557.524   Mean   : 565.416  
#>  3rd Qu.:  87.536   3rd Qu.:  97.848   3rd Qu.: 110.228   3rd Qu.: 112.237  
#>  Max.   :3601.498   Max.   :3624.913   Max.   :3649.043   Max.   :3673.902  
#>       V45                V46                V47                V48          
#>  Min.   :   1.587   Min.   :   1.576   Min.   :   1.565   Min.   :   1.554  
#>  1st Qu.:   3.316   1st Qu.:   4.081   1st Qu.:   4.729   1st Qu.:   6.076  
#>  Median :  35.453   Median :  38.079   Median :  47.903   Median :  51.200  
#>  Mean   : 574.828   Mean   : 585.320   Mean   : 596.708   Mean   : 608.935  
#>  3rd Qu.: 119.717   3rd Qu.: 121.762   3rd Qu.: 131.399   3rd Qu.: 148.533  
#>  Max.   :3699.485   Max.   :3725.804   Max.   :3752.856   Max.   :3780.654  
#>       V49                V50                V51                V52          
#>  Min.   :   1.545   Min.   :   1.536   Min.   :   1.527   Min.   :   1.519  
#>  1st Qu.:   7.375   1st Qu.:   8.791   1st Qu.:  11.218   1st Qu.:  13.211  
#>  Median :  63.265   Median :  68.593   Median :  78.798   Median :  82.741  
#>  Mean   : 622.068   Mean   : 636.139   Mean   : 651.285   Mean   : 667.586  
#>  3rd Qu.: 197.861   3rd Qu.: 206.180   3rd Qu.: 304.712   3rd Qu.: 447.554  
#>  Max.   :3809.192   Max.   :3838.483   Max.   :3868.523   Max.   :3899.321  
#>       V53                V54                V55                V56          
#>  Min.   :   1.511   Min.   :   1.504   Min.   :   1.497   Min.   :   1.491  
#>  1st Qu.:  19.048   1st Qu.:  22.526   1st Qu.:  23.898   1st Qu.:  32.387  
#>  Median : 101.155   Median : 108.601   Median : 118.707   Median : 137.533  
#>  Mean   : 685.188   Mean   : 704.241   Mean   : 724.902   Mean   : 747.354  
#>  3rd Qu.: 483.243   3rd Qu.: 594.034   3rd Qu.: 598.674   3rd Qu.: 685.375  
#>  Max.   :3930.876   Max.   :3963.197   Max.   :3996.282   Max.   :4030.139  
#>       V57                V58                V59                V60         
#>  Min.   :   1.485   Min.   :   1.479   Min.   :   1.474   Min.   :   1.47  
#>  1st Qu.:  43.337   1st Qu.:  54.356   1st Qu.:  69.301   1st Qu.:  89.53  
#>  Median : 139.402   Median : 148.246   Median : 168.336   Median : 200.15  
#>  Mean   : 771.815   Mean   : 798.524   Mean   : 827.722   Mean   : 859.70  
#>  3rd Qu.: 714.723   3rd Qu.: 784.951   3rd Qu.: 842.945   3rd Qu.: 906.32  
#>  Max.   :4064.765   Max.   :4100.171   Max.   :4136.353   Max.   :4173.32  
#>       V61                V62                V63                V64          
#>  Min.   :   1.465   Min.   :   1.461   Min.   :   1.457   Min.   :   1.454  
#>  1st Qu.: 112.621   1st Qu.: 114.157   1st Qu.: 115.698   1st Qu.: 117.237  
#>  Median : 234.505   Median : 261.070   Median : 340.962   Median : 405.184  
#>  Mean   : 894.769   Mean   : 933.248   Mean   : 975.468   Mean   :1021.771  
#>  3rd Qu.:1000.578   3rd Qu.:1058.737   3rd Qu.:1195.533   3rd Qu.:1252.970  
#>  Max.   :4211.068   Max.   :4249.607   Max.   :4288.935   Max.   :4329.057  
#>       V65                V66                V67                V68          
#>  Min.   :   1.451   Min.   :   1.448   Min.   :   1.445   Min.   :   1.442  
#>  1st Qu.: 118.769   1st Qu.: 120.287   1st Qu.: 121.785   1st Qu.: 123.257  
#>  Median : 465.266   Median : 558.278   Median : 654.766   Median : 757.374  
#>  Mean   :1072.469   Mean   :1127.860   Mean   :1188.157   Mean   :1253.463  
#>  3rd Qu.:1436.228   3rd Qu.:1502.611   3rd Qu.:1732.418   3rd Qu.:1823.947  
#>  Max.   :4369.973   Max.   :4411.689   Max.   :4454.203   Max.   :4497.520  
#>       V69               V70                V71                V72          
#>  Min.   :   1.44   Min.   :   1.438   Min.   :   1.436   Min.   :   1.434  
#>  1st Qu.: 124.70   1st Qu.: 126.098   1st Qu.: 127.453   1st Qu.: 128.757  
#>  Median : 912.74   Median :1075.749   Median :1116.818   Median :1144.261  
#>  Mean   :1323.74   Mean   :1398.737   Mean   :1477.951   Mean   :1560.498  
#>  3rd Qu.:2094.64   3rd Qu.:2234.541   3rd Qu.:2434.534   3rd Qu.:2619.694  
#>  Max.   :4541.64   Max.   :4586.568   Max.   :4632.302   Max.   :4678.845  
#>       V73                V74                V75                V76          
#>  Min.   :   1.433   Min.   :   1.431   Min.   :   1.429   Min.   :   1.428  
#>  1st Qu.: 129.721   1st Qu.: 130.647   1st Qu.: 131.578   1st Qu.: 132.513  
#>  Median :1144.255   Median :1144.320   Median :1162.406   Median :1178.114  
#>  Mean   :1645.117   Mean   :1730.074   Mean   :1813.108   Mean   :1891.465  
#>  3rd Qu.:2770.448   3rd Qu.:3125.762   3rd Qu.:3399.191   3rd Qu.:3760.919  
#>  Max.   :4726.198   Max.   :4774.362   Max.   :5030.859   Max.   :5389.889  
#>       V77                V78                V79                V80          
#>  Min.   :   1.427   Min.   :   1.426   Min.   :   1.425   Min.   :   1.424  
#>  1st Qu.: 133.453   1st Qu.: 134.397   1st Qu.: 135.344   1st Qu.: 136.294  
#>  Median :1191.167   Median :1201.329   Median :1208.407   Median :1212.290  
#>  Mean   :1961.967   Mean   :2021.235   Mean   :2066.035   Mean   :2093.777  
#>  3rd Qu.:3915.022   3rd Qu.:3946.873   3rd Qu.:4077.628   3rd Qu.:4196.836  
#>  Max.   :5655.894   Max.   :5973.680   Max.   :6201.373   Max.   :6316.464  
#>       V81                V82                V83                V84         
#>  Min.   :   1.423   Min.   :   1.422   Min.   :   1.421   Min.   :   1.42  
#>  1st Qu.: 137.246   1st Qu.: 137.963   1st Qu.: 138.440   1st Qu.: 138.84  
#>  Median :1212.928   Median :1210.252   Median :1204.377   Median :1195.38  
#>  Mean   :2102.965   Mean   :2093.477   Mean   :2066.661   Mean   :2024.99  
#>  3rd Qu.:4215.605   3rd Qu.:4147.061   3rd Qu.:4120.861   3rd Qu.:4001.07  
#>  Max.   :6320.794   Max.   :6214.535   Max.   :5996.082   Max.   :5671.47  
#>       V85               V86                V87                V88          
#>  Min.   :   1.42   Min.   :   1.419   Min.   :   1.419   Min.   :   1.418  
#>  1st Qu.: 139.15   1st Qu.: 139.388   1st Qu.: 139.549   1st Qu.: 139.639  
#>  Median :1183.43   Median :1168.769   Median :1151.672   Median :1144.420  
#>  Mean   :1971.62   Mean   :1909.966   Mean   :1843.231   Mean   :1774.244  
#>  3rd Qu.:3777.20   3rd Qu.:3448.609   3rd Qu.:3161.767   3rd Qu.:3040.808  
#>  Max.   :5412.23   Max.   :5415.681   Max.   :5474.382   Max.   :5533.881  
#>       V89                V90                V91                V92          
#>  Min.   :   1.418   Min.   :   1.418   Min.   :   1.418   Min.   :   1.418  
#>  1st Qu.: 139.663   1st Qu.: 139.624   1st Qu.: 139.528   1st Qu.: 139.380  
#>  Median :1144.418   Median :1130.937   Median :1090.345   Median : 942.117  
#>  Mean   :1705.332   Mean   :1638.297   Mean   :1574.454   Mean   :1514.728  
#>  3rd Qu.:2790.528   3rd Qu.:2552.675   3rd Qu.:2271.888   3rd Qu.:2094.746  
#>  Max.   :5594.179   Max.   :5655.267   Max.   :5717.150   Max.   :5779.812  
#>       V93                V94                V95                V96          
#>  Min.   :   1.418   Min.   :   1.418   Min.   :   1.419   Min.   :   1.419  
#>  1st Qu.: 139.186   1st Qu.: 138.953   1st Qu.: 136.504   1st Qu.: 133.820  
#>  Median : 780.240   Median : 697.259   Median : 609.809   Median : 509.371  
#>  Mean   :1459.646   Mean   :1409.501   Mean   :1364.342   Mean   :1324.071  
#>  3rd Qu.:1857.122   3rd Qu.:1732.490   3rd Qu.:1532.694   3rd Qu.:1436.267  
#>  Max.   :5843.260   Max.   :5907.478   Max.   :5972.470   Max.   :6038.220  
#>       V97               V98                V99                V100         
#>  Min.   :   1.42   Min.   :   1.421   Min.   :   1.422   Min.   :   1.424  
#>  1st Qu.: 131.07   1st Qu.: 128.267   1st Qu.: 125.434   1st Qu.: 117.973  
#>  Median : 418.84   Median : 355.051   Median : 272.618   Median : 248.107  
#>  Mean   :1288.47   Mean   :1257.257   Mean   :1230.109   Mean   :1206.693  
#>  3rd Qu.:1280.85   3rd Qu.:1195.492   3rd Qu.:1085.066   3rd Qu.:1000.501  
#>  Max.   :6104.73   Max.   :6171.986   Max.   :6239.988   Max.   :6308.714  
#>       V101               V102               V103              V104         
#>  Min.   :   1.425   Min.   :   1.427   Min.   :   1.43   Min.   :   1.432  
#>  1st Qu.:  97.890   1st Qu.:  75.588   1st Qu.:  58.53   1st Qu.:  46.251  
#>  Median : 220.847   Median : 208.194   Median : 196.89   Median : 177.229  
#>  Mean   :1186.658   Mean   :1169.681   Mean   :1155.46   Mean   :1143.701  
#>  3rd Qu.: 931.454   3rd Qu.: 871.224   3rd Qu.: 864.93   3rd Qu.: 842.624  
#>  Max.   :6378.171   Max.   :6448.332   Max.   :6519.20   Max.   :6590.754  
#>       V105               V106               V107               V108         
#>  Min.   :   1.435   Min.   :   1.438   Min.   :   1.442   Min.   :   1.446  
#>  1st Qu.:  38.278   1st Qu.:  29.436   1st Qu.:  23.845   1st Qu.:  22.102  
#>  Median : 160.334   Median : 153.482   Median : 133.491   Median : 125.480  
#>  Mean   :1134.158   Mean   :1126.585   Mean   :1120.772   Mean   :1116.549  
#>  3rd Qu.: 740.223   3rd Qu.: 714.546   3rd Qu.: 645.885   3rd Qu.: 575.384  
#>  Max.   :6662.991   Max.   :6735.881   Max.   :6809.428   Max.   :6883.596  
#>       V109              V110               V111              V112         
#>  Min.   :   1.45   Min.   :   1.455   Min.   :   1.46   Min.   :   1.466  
#>  1st Qu.:  16.05   1st Qu.:  13.005   1st Qu.:  10.69   1st Qu.:   9.552  
#>  Median : 101.57   Median :  96.678   Median :  72.87   Median :  70.227  
#>  Mean   :1113.73   Mean   :1112.206   Mean   :1111.82   Mean   :1112.506  
#>  3rd Qu.: 491.35   3rd Qu.: 321.807   3rd Qu.: 220.84   3rd Qu.: 219.641  
#>  Max.   :6958.39   Max.   :7033.762   Max.   :7109.72   Max.   :7186.214  
#>       V113               V114               V115               V116         
#>  Min.   :   1.472   Min.   :   1.478   Min.   :   1.485   Min.   :   1.493  
#>  1st Qu.:   8.477   1st Qu.:   7.907   1st Qu.:   8.735   1st Qu.:   9.255  
#>  Median :  62.523   Median :  58.193   Median :  49.175   Median :  47.668  
#>  Mean   :1114.148   Mean   :1116.707   Mean   :1120.155   Mean   :1124.555  
#>  3rd Qu.: 211.446   3rd Qu.: 207.643   3rd Qu.: 201.895   3rd Qu.: 198.371  
#>  Max.   :7263.244   Max.   :7340.758   Max.   :7418.748   Max.   :7497.158  
#>       V117               V118               V119               V120         
#>  Min.   :   1.501   Min.   :   1.509   Min.   :   1.518   Min.   :   1.528  
#>  1st Qu.:  10.220   1st Qu.:  10.473   1st Qu.:  11.321   1st Qu.:  12.617  
#>  Median :  36.396   Median :  34.290   Median :  30.744   Median :  37.364  
#>  Mean   :1130.142   Mean   :1137.424   Mean   :1146.722   Mean   :1157.719  
#>  3rd Qu.: 180.500   3rd Qu.: 167.729   3rd Qu.: 137.381   3rd Qu.: 131.828  
#>  Max.   :7575.972   Max.   :7655.127   Max.   :7734.597   Max.   :7814.308  
#>       V121               V122               V123              V124         
#>  Min.   :   1.538   Min.   :   1.549   Min.   :   1.56   Min.   :   1.572  
#>  1st Qu.:  12.910   1st Qu.:  13.536   1st Qu.:  14.72   1st Qu.:  16.348  
#>  Median :  46.016   Median :  54.067   Median :  60.28   Median :  61.155  
#>  Mean   :1170.398   Mean   :1185.076   Mean   :1202.18   Mean   :1222.543  
#>  3rd Qu.: 130.597   3rd Qu.: 129.388   3rd Qu.: 131.50   3rd Qu.: 188.079  
#>  Max.   :7894.225   Max.   :7974.258   Max.   :8054.36   Max.   :8134.411  
#>       V125               V126               V127               V128         
#>  Min.   :   1.584   Min.   :   1.597   Min.   :   1.611   Min.   :   1.625  
#>  1st Qu.:  16.811   1st Qu.:  21.799   1st Qu.:  22.604   1st Qu.:  22.322  
#>  Median :  63.897   Median :  66.222   Median :  73.909   Median : 104.410  
#>  Mean   :1247.630   Mean   :1279.805   Mean   :1322.682   Mean   :1381.569  
#>  3rd Qu.: 219.880   3rd Qu.: 330.558   3rd Qu.: 503.957   3rd Qu.: 747.478  
#>  Max.   :8214.348   Max.   :8294.033   Max.   :8373.362   Max.   :8452.160  
#>       V129               V130               V131              V132         
#>  Min.   :   1.639   Min.   :   1.655   Min.   :   1.67   Min.   :   1.687  
#>  1st Qu.:  24.653   1st Qu.:  24.207   1st Qu.:  25.55   1st Qu.:  25.794  
#>  Median : 123.903   Median : 128.717   Median : 158.54   Median : 171.190  
#>  Mean   :1463.897   Mean   :1579.316   Mean   :1739.27   Mean   :1956.344  
#>  3rd Qu.:1166.349   3rd Qu.:1633.697   3rd Qu.:2197.44   3rd Qu.:3269.524  
#>  Max.   :8530.275   Max.   :8607.481   Max.   :8683.56   Max.   :8758.185  
#>       V133               V134               V135                V136          
#>  Min.   :   1.703   Min.   :   1.721   Min.   :    1.739   Min.   :    1.757  
#>  1st Qu.:  25.543   1st Qu.:  25.125   1st Qu.:   26.472   1st Qu.:   27.740  
#>  Median : 186.315   Median : 185.953   Median :  185.630   Median :  185.626  
#>  Mean   :2241.440   Mean   :2594.836   Mean   : 3000.368   Mean   : 3422.207  
#>  3rd Qu.:4888.651   3rd Qu.:5828.310   3rd Qu.: 6981.835   3rd Qu.: 7219.103  
#>  Max.   :8831.042   Max.   :9638.297   Max.   :12844.565   Max.   :16740.217  
#>       V137                V138                V139          
#>  Min.   :    1.776   Min.   :    1.796   Min.   :    1.816  
#>  1st Qu.:   27.027   1st Qu.:   26.373   1st Qu.:   25.717  
#>  Median :  185.447   Median :  191.153   Median :  197.810  
#>  Mean   : 3803.909   Mean   : 4098.556   Mean   : 4289.860  
#>  3rd Qu.: 7788.821   3rd Qu.: 7829.436   3rd Qu.: 7666.380  
#>  Max.   :21212.993   Max.   :25971.526   Max.   :28633.465  
#>       V140                V141                V142          
#>  Min.   :    1.836   Min.   :    1.857   Min.   :    1.879  
#>  1st Qu.:   25.649   1st Qu.:   25.770   1st Qu.:   25.648  
#>  Median :  196.899   Median :  195.949   Median :  194.956  
#>  Mean   : 4381.049   Mean   : 4402.867   Mean   : 4396.621  
#>  3rd Qu.: 7475.335   3rd Qu.: 7677.157   3rd Qu.: 7254.192  
#>  Max.   :29305.722   Max.   :30873.216   Max.   :31368.854  
#>       V143                V144                V145                V146         
#>  Min.   :    1.901   Min.   :    1.923   Min.   :    1.946   Min.   :    1.97  
#>  1st Qu.:   25.334   1st Qu.:   25.481   1st Qu.:   26.146   1st Qu.:   26.35  
#>  Median :  193.920   Median :  192.838   Median :  191.709   Median :  190.53  
#>  Mean   : 4373.255   Mean   : 4323.789   Mean   : 4252.452   Mean   : 4170.11  
#>  3rd Qu.: 7033.699   3rd Qu.: 6356.331   3rd Qu.: 6717.721   3rd Qu.: 5629.20  
#>  Max.   :30651.818   Max.   :29364.239   Max.   :28179.034   Max.   :28436.97  
#>       V147                V148                V149          
#>  Min.   :    1.994   Min.   :    2.018   Min.   :    2.043  
#>  1st Qu.:   25.863   1st Qu.:   25.423   1st Qu.:   25.194  
#>  Median :  189.303   Median :  191.382   Median :  195.130  
#>  Mean   : 4079.233   Mean   : 3992.754   Mean   : 3943.573  
#>  3rd Qu.: 5557.453   3rd Qu.: 4999.610   3rd Qu.: 4697.098  
#>  Max.   :27574.109   Max.   :26197.232   Max.   :24668.008  
#>       V150                V151                V152          
#>  Min.   :    2.069   Min.   :    2.095   Min.   :    2.121  
#>  1st Qu.:   25.587   1st Qu.:   25.614   1st Qu.:   25.459  
#>  Median :  198.682   Median :  202.021   Median :  205.133  
#>  Mean   : 3957.649   Mean   : 4051.807   Mean   : 4259.549  
#>  3rd Qu.: 4311.622   3rd Qu.: 5173.409   3rd Qu.: 6190.179  
#>  Max.   :24111.621   Max.   :23095.469   Max.   :22079.713  
#>       V153                V154                V155          
#>  Min.   :    2.148   Min.   :    2.176   Min.   :    2.204  
#>  1st Qu.:   25.339   1st Qu.:   25.300   1st Qu.:   25.357  
#>  Median :  208.007   Median :  210.634   Median :  213.006  
#>  Mean   : 4644.601   Mean   : 5301.757   Mean   : 6352.947  
#>  3rd Qu.: 8394.702   3rd Qu.:11321.783   3rd Qu.:11338.528  
#>  Max.   :21315.765   Max.   :20957.676   Max.   :27496.361  
#>       V156                V157                V158          
#>  Min.   :    2.232   Min.   :    2.261   Min.   :     2.29  
#>  1st Qu.:   25.353   1st Qu.:   25.412   1st Qu.:    25.52  
#>  Median :  215.119   Median :  216.969   Median :   218.56  
#>  Mean   : 7934.985   Mean   :10161.739   Mean   : 13039.05  
#>  3rd Qu.:11378.808   3rd Qu.:11430.062   3rd Qu.: 11481.36  
#>  Max.   :51110.886   Max.   :93564.060   Max.   :161028.05  
#>       V159                V160          
#>  Min.   :     2.32   Min.   :     2.35  
#>  1st Qu.:    25.68   1st Qu.:    25.85  
#>  Median :   219.89   Median :   220.97  
#>  Mean   : 16344.42   Mean   : 19580.04  
#>  3rd Qu.: 11524.53   3rd Qu.: 11555.20  
#>  Max.   :254493.47   Max.   :361745.70
```

## Why numerical conditioning is explicit

At each time–scale cell, the improved computation works with a local
complex spectral matrix. Highly redundant controls can make the
conditioning block singular or ill-conditioned. The package therefore
records condition number, local rank, instability, and whether a
regularization policy was used.

`method = "none"` does not silently rescue unstable matrices. `"ridge"`
and `"svd"` are explicit analysis decisions and remain in the result
provenance.

## Surrogate and field-aware significance in 0.3.0

Improved PWC can now generate response surrogates under an explicit
`AwiNullSpec`. The focal predictor and controls remain fixed, which
preserves their observed joint structure while the response association
is broken under the selected null. Empirical p-values are then treated
as a scale-by-time field, not as unrelated cellwise tests.

``` r

null <- awi_null_spec(
  method = "iaaft",
  n_surrogates = 499,
  seed = 20260904,
  field_method = "BY"
)

fit_sig <- awi_pwc(
  yld, enso, controls = list(rain, temp),
  condition = awi_condition_spec(method = "svd"),
  significance = TRUE,
  null = null
)
```

Cells inside the cone of influence are excluded from the inferential
domain. The surrogate method is part of the null hypothesis and should
be selected from the time-series structure before interpreting the
resulting significance map.

## Interpretation

A persistent partial coherence can strengthen an attribution argument by
showing that a focal association survives specified controls. It cannot
demonstrate that the focal predictor caused the response. Omitted
drivers, shared forcing, measurement error, nonstationarity, and
model/design choices remain relevant.

## References

Hu, Wei, and Bing Si. 2021. “Technical Note: Improved Partial Wavelet
Coherency for Understanding Scale-Specific and Localized Bivariate
Relationships in Geosciences.” *Hydrology and Earth System Sciences* 25:
321–31. <https://doi.org/10.5194/hess-25-321-2021>.

Wang, Hui, Xiaohan Lin, Peichao Gao, et al. 2026. “Divergent Responses
of Crop Yields to Meteorological and Agricultural Drought
Characteristics.” *Agricultural Water Management* 331: 110443.
<https://doi.org/10.1016/j.agwat.2026.110443>.
