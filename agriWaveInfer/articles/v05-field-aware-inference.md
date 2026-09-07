# Field-Aware Inference: FDR and Cluster Tests in the Time-Frequency Plane

## From pointwise p-values to a field

A scale-by-time coherence surface can contain thousands of candidate
cells. Interpreting each `p(s,t)` independently inflates the opportunity
for false positive patches. Version 0.3.0 therefore treats the
inferential domain as a field and excludes cells inside the cone of
influence from native PWC/MWC significance.

## Empirical Monte Carlo p-values

For an observed statistic `T(s,t)` and `B` surrogate fields, the
one-sided empirical p-value is

``` math
p(s,t)=\frac{1+\sum_{b=1}^{B}I\{T_b(s,t)\geq T(s,t)\}}{B+1}.
```

The +1 correction prevents zero p-values and makes the finite surrogate
resolution explicit.

## False discovery rate

[`awi_fdr()`](https://wep69.github.io/agriWaveInfer/reference/awi_fdr.md)
implements Benjamini-Hochberg and Benjamini-Yekutieli adjustment
(Benjamini and Hochberg 1995; Benjamini and Yekutieli 2001). BY is more
conservative and is available when arbitrary dependence among tests is a
concern.

``` r

p <- matrix(c(
  0.001, 0.010, 0.25,
  0.020, 0.400, 0.90
), nrow = 2)

awi_fdr(p, alpha = 0.05, method = "BH")
#> <awi_fdr>
#>  method: BH 
#>  tests: 6 
#>  significant: 3 at alpha = 0.05
awi_fdr(p, alpha = 0.05, method = "BY")
#> <awi_fdr>
#>  method: BY 
#>  tests: 6 
#>  significant: 1 at alpha = 0.05
```

## Cluster-level inference

Coherent wavelet features often occupy contiguous scale-time regions. A
cluster-based test first defines supra-threshold cells, groups connected
cells, and uses the maximum cluster score from each surrogate field as
the null reference. Version 0.3.0 provides cluster mass and cluster size
with four- or eight-neighbour connectivity. The logic follows the
general maximum-cluster permutation framework (Maris and Oostenveld
2007).

``` r

observed <- matrix(0, 12, 18)
observed[4:7, 7:12] <- 2
null_fields <- array(0, dim = c(12, 18, 99))

ct <- awi_cluster_test(
  observed,
  null_fields,
  threshold = 1,
  statistic = "mass",
  connectivity = 8
)
ct
#> <awi_cluster_test>
#>  statistic: mass 
#>  connectivity: 8 
#>  surrogate fields: 99 
#>  clusters: 1 
#>  significant clusters: 1
```

A significant cluster supports a field-level departure from the null. It
does not imply that every individual cell is separately significant, nor
that the exact onset, offset, or boundary is estimated with
cluster-level precision.

## Native PWC and MWC integration

``` r

null <- awi_null_spec(
  method = "iaaft",
  n_surrogates = 499,
  seed = 20260904,
  field_method = "cluster_mass",
  alpha = 0.05
)

pwc <- awi_pwc(
  yield, enso, controls = list(rainfall, temperature),
  condition = awi_condition_spec(method = "svd"),
  significance = TRUE,
  null = null
)

pwc$p_value
pwc$significance
pwc$field_result$clusters
```

For improved PWC and native MWC, the response is surrogated while
predictors and controls remain fixed. This preserves the observed
dependence structure among the explanatory series. The choice is
explicit in the result provenance and is not a causal randomization
design.

## Interpretation guardrails

Field correction reduces false-positive interpretation; it does not
repair an inappropriate null model, poor temporal resolution,
nonstationarity, edge effects, omitted confounding, or unstable local
spectral matrices. Inference should therefore be read jointly with the
null specification, COI, numerical conditioning fields, and the original
scientific design.

## References

Benjamini, Yoav, and Yosef Hochberg. 1995. “Controlling the False
Discovery Rate: A Practical and Powerful Approach to Multiple Testing.”
*Journal of the Royal Statistical Society Series B* 57 (1): 289–300.
<https://doi.org/10.1111/j.2517-6161.1995.tb02031.x>.

Benjamini, Yoav, and Daniel Yekutieli. 2001. “The Control of the False
Discovery Rate in Multiple Testing Under Dependency.” *The Annals of
Statistics* 29 (4): 1165–88. <https://doi.org/10.1214/aos/1013699998>.

Maris, Eric, and Robert Oostenveld. 2007. “Nonparametric Statistical
Testing of EEG- and MEG-Data.” *Journal of Neuroscience Methods* 164
(1): 177–90. <https://doi.org/10.1016/j.jneumeth.2007.03.024>.
