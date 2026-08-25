# Correlation among RGB indices

Correlation among RGB indices

## Usage

``` r
pheno_rgb_cor(
  x,
  indices = c("ExG", "NGRDI", "GLI", "VARI", "RGBVI", "MGRVI"),
  mask = NULL,
  method = c("pearson", "spearman", "kendall")
)
```

## Arguments

- x:

  RGB input.

- indices:

  Index names.

- mask:

  Optional image mask.

- method:

  Correlation method.

## Value

Correlation matrix.

## Examples

``` r
pheno_rgb_cor(pheno_data("leaf_rgb"), c("ExG","NGRDI","GLI","VARI"))
#>             ExG     NGRDI       GLI      VARI
#> ExG   1.0000000 0.9943466 0.9996626 0.9995133
#> NGRDI 0.9943466 1.0000000 0.9922803 0.9966984
#> GLI   0.9996626 0.9922803 1.0000000 0.9990129
#> VARI  0.9995133 0.9966984 0.9990129 1.0000000
pheno_rgb_cor(pheno_data("leaf_rgb"), c("ExG","TGI","CIVE"), pheno_data("leaf_mask"))
#>             ExG        TGI       CIVE
#> ExG   1.0000000  0.9652597 -0.9731248
#> TGI   0.9652597  1.0000000 -0.9992770
#> CIVE -0.9731248 -0.9992770  1.0000000
pheno_rgb_cor(data.frame(R=1:10,G=10:1,B=rep(5,10)), c("GRD","NGRDI"), method="spearman")
#>       GRD NGRDI
#> GRD     1     1
#> NGRDI   1     1
```
