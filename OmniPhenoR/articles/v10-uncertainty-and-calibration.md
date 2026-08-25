# OmniPhenoR Predictive Uncertainty and Probability Calibration

## 1. Purpose

A hard prediction hides how strongly a model supported its decision.
OmniPhenoR 0.2.0 retains probabilities and exposes simple uncertainty
and calibration diagnostics so that a leaf classified with probability
0.51 is not treated as equivalent to one classified with probability
0.99.

Uncertainty and calibration answer different questions. Uncertainty
summarizes how diffuse a probability distribution is for an observation.
Calibration asks whether predicted confidence agrees with empirical
correctness across many observations.

## 2. Entropy uncertainty

``` r

probs <- list(
  confident=c(.99,.01),
  moderate=c(.75,.25),
  ambiguous=c(.50,.50)
)
sapply(probs, pheno_uncertainty, method="entropy")
#>  confident   moderate  ambiguous 
#> 0.08079314 0.81127812 1.00000000
```

Normalized entropy is close to zero for concentrated probability and
approaches one for equal probabilities across classes.

## 3. Margin uncertainty

``` r

sapply(probs, pheno_uncertainty, method="margin")
#> confident  moderate ambiguous 
#>      0.02      0.50      1.00
```

Margin uncertainty is high when the two largest class probabilities are
close. It is easy to interpret for multiclass classifiers where two
candidate classes compete.

## 4. Confidence uncertainty

``` r

sapply(probs, pheno_uncertainty, method="confidence")
#> confident  moderate ambiguous 
#>      0.01      0.25      0.50
```

This is based on one minus maximum class probability. It is simple but
does not distinguish all shapes of a multiclass distribution.

## 5. Pixel-wise uncertainty

Segmentation probabilities can be analyzed at every pixel.

``` r

ml <- pheno_validation_data("ml")
u_confident <- pheno_uncertainty(ml$probability, "entropy")
u_ambiguous <- pheno_uncertainty(ml$ambiguous_probability, "entropy")

c(
  mean_confident=mean(u_confident),
  mean_ambiguous=mean(u_ambiguous)
)
#> mean_confident mean_ambiguous 
#>       0.286397       1.000000
attr(u_confident, "summary")
#>     mean   median      q95 
#> 0.286397 0.286397 0.286397
```

A spatial uncertainty map can highlight boundaries, overlapping organs,
unusual lesions, glare, shadows, or out-of-domain regions. It should be
treated as a diagnostic rather than a proof of error.

## 6. A model can be confidently wrong

Consider a classifier that gives 0.99 probability to the wrong class.
Its entropy is low even though the prediction is incorrect. Uncertainty
must therefore be evaluated together with held-out truth.

``` r

pheno_uncertainty(c(wrong=.99, correct=.01), "entropy")
#> [1] 0.08079314
```

This is why calibration and external validation matter.

## 7. Calibration of binary probabilities

Modern neural networks can have confidence values that do not match
observed accuracy ([Guo et al. 2017](#ref-Guo2017_Calibration)).

``` r

truth <- c(0,1,1,0,1,0,1,0,1,0,1,0)
p <- c(.05,.92,.70,.12,.82,.30,.63,.18,.95,.42,.66,.08)
cal <- pheno_probability_calibration(truth, p, bins=4)
cal$metrics
#> # A tibble: 1 × 4
#>   brier log_loss   ece accuracy
#>   <dbl>    <dbl> <dbl>    <dbl>
#> 1 0.118    0.244 0.206        1
cal$calibration
#> # A tibble: 4 × 6
#>     bin lower upper     n mean_confidence accuracy
#>   <int> <dbl> <dbl> <int>           <dbl>    <dbl>
#> 1     1  0     0.25     0          NA           NA
#> 2     2  0.25  0.5      0          NA           NA
#> 3     3  0.5   0.75     5           0.654        1
#> 4     4  0.75  1        7           0.894        1
```

The returned metrics include:

- **Brier score:** squared probability error;
- **log loss:** strongly penalizes confident probability assigned away
  from the true class;
- **ECE:** expected calibration error based on confidence bins;
- **accuracy:** included for context.

## 8. Calibration of multiclass probabilities

``` r

pr <- rbind(
  c(.80,.15,.05),
  c(.20,.65,.15),
  c(.08,.20,.72),
  c(.70,.20,.10),
  c(.22,.62,.16),
  c(.05,.25,.70),
  c(.55,.35,.10),
  c(.20,.55,.25),
  c(.10,.35,.55)
)
truth3 <- rep(c("healthy","chlorotic","necrotic"),3)
cal3 <- pheno_probability_calibration(
  truth3,
  pr,
  classes=c("healthy","chlorotic","necrotic"),
  bins=3
)
cal3$metrics
#> # A tibble: 1 × 4
#>   brier log_loss   ece accuracy
#>   <dbl>    <dbl> <dbl>    <dbl>
#> 1 0.207    0.441 0.351        1
cal3$calibration
#> # A tibble: 3 × 6
#>     bin lower upper     n mean_confidence accuracy
#>   <int> <dbl> <dbl> <int>           <dbl>    <dbl>
#> 1     1 0     0.333     0          NA           NA
#> 2     2 0.333 0.667     5           0.584        1
#> 3     3 0.667 1         4           0.73         1
```

## 9. Why ECE needs context

ECE depends on binning and sample size. A small test set can give
unstable calibration estimates, and an apparently good global ECE can
hide poor calibration within an important disease class. For
publication, complement a scalar calibration metric with a reliability
diagram or class-specific analysis when sample size permits.

## 10. Probability thresholds are decisions

Segmentation and classification thresholds convert a probability into an
action. The scientifically appropriate threshold depends on
consequences.

For disease scouting, false negatives may be more costly than false
positives. For automated spraying, false positives may have material and
environmental costs. For quantitative lesion area, the threshold should
minimize measurement error rather than operational classification error.

``` r

p_disease <- .72
if (p_disease >= .80) {
  "automatic intervention"
} else if (p_disease >= .50) {
  "manual review"
} else {
  "no flag"
}
#> [1] "manual review"
```

Thresholds should be frozen using validation data before final test
evaluation.

## 11. Segmentation threshold sensitivity

``` r

img <- pheno_data("leaf_rgb")
truth_mask <- pheno_data("leaf_mask")
score_fun <- function(z) {
  exg <- pheno_rgb_indices(z,"ExG")[,,1]
  plogis(20*(exg-median(exg)))
}
pred <- pheno_segment_dl(img, score_fun, classes=c("background","leaf"), qc=FALSE)
leaf_p <- pred$probability[, , "leaf"]

do.call(rbind, lapply(c(.3,.5,.7), function(t) {
  data.frame(threshold=t, pheno_segmentation_metrics(truth_mask, leaf_p>=t))
}))
#>   threshold   tp   tn   fp fn      iou      dice precision recall specificity
#> 1       0.3 2872    0 9416  0 0.233724 0.3788918  0.233724      1           0
#> 2       0.5 2872    0 9416  0 0.233724 0.3788918  0.233724      1           0
#> 3       0.7 2872 9416    0  0 1.000000 1.0000000  1.000000      1           1
#>          f1 balanced_accuracy area_bias boundary_disagreement
#> 1 0.3788918               0.5  0.766276            0.03222656
#> 2 0.3788918               0.5  0.766276            0.03222656
#> 3 1.0000000               1.0  0.000000            0.00000000
```

A threshold can improve overlap while worsening area bias. The correct
target metric should reflect the final phenotype.

## 12. Dropout-based uncertainty and the package boundary

Monte Carlo dropout is one approach to approximate predictive
uncertainty in neural networks ([Gal and Ghahramani
2016](#ref-Gal2016_Dropout)). OmniPhenoR 0.2.0 provides generic
uncertainty summaries for probability outputs but does not silently
activate stochastic dropout inference. A custom `torch` workflow can
produce repeated probability draws and then summarize their disagreement
while preserving model metadata.

``` r

# Conceptual outline for a custom model whose dropout layers remain active:
# draws <- replicate(B, predict_probability(model, image, dropout_active=TRUE), simplify=FALSE)
# pixel_sd <- apply(array_of_draws, c(1,2,3), sd)
# mean_probability <- apply(array_of_draws, c(1,2,3), mean)
```

The distinction matters because entropy of one softmax output is not the
same quantity as uncertainty across stochastic model draws.

## 13. Ensemble disagreement

An ensemble can provide another view of model uncertainty by comparing
predictions from independently trained models. The package does not
reduce ensemble variation to a universal number because the appropriate
summary depends on task and ensemble design. Probability arrays can,
however, be retained and summarized externally.

## 14. Uncertainty-guided review

A practical workflow can define three regions:

``` text
low uncertainty     -> accept automatically
intermediate        -> retain but flag
high uncertainty    -> manual review or reacquisition
```

The thresholds for those regions should be validated against actual
error. Otherwise an uncertainty score becomes another uncalibrated
quantity.

## 15. Acquisition QC and model uncertainty

Image quality can be a cause of uncertainty.

``` r

pheno_qc(img)
#> # A tibble: 1 × 12
#>   height width finite_fraction missing_fraction    min   max dynamic_range  mean
#>    <int> <int>           <dbl>            <dbl>  <dbl> <dbl>         <dbl> <dbl>
#> 1     96   128               1                0 0.0769  0.86         0.783 0.649
#> # ℹ 4 more variables: sd <dbl>, low_saturation_fraction <dbl>,
#> #   high_saturation_fraction <dbl>, gradient_energy <dbl>
```

Blur, saturation, poor dynamic range, or missing tissue may increase
predictive ambiguity. A useful validation study links uncertainty to QC
variables and checks whether uncertainty identifies problematic
acquisitions.

## 16. Domain shift

A model may become overconfident on images from a new camera or
environment. Calibration measured on the development test set does not
automatically transfer to a new domain. External calibration analysis
should therefore accompany external validation when probabilities are
used operationally.

## 17. Disease example

Suppose each leaf has both a disease class and a segmented lesion
percentage. The class probability can support triage, while lesion
severity remains the quantitative research trait. If uncertain
classifications cluster around low lesion severity, that may be
biologically reasonable. If they cluster by camera or date, the
uncertainty may reveal acquisition-domain instability.

## 18. Calibration is not physiological calibration

Probability calibration should not be confused with calibrating an RGB
index against chlorophyll, water status, nutrient concentration, or
another physiological variable. The former concerns predicted class
confidence. The latter concerns measurement validity against an
independent biological reference.

## 19. Reporting checklist

When probabilities are interpreted, report class order,
probability-generation method, calibration dataset, whether calibration
data were separate from final testing, Brier/log-loss/ECE or other
diagnostics, reliability plots when possible, threshold selection rule,
uncertainty definition, rejection/manual-review rule, domain-shift
evaluation, and whether uncertainty was predictive of actual error.

## 20. Final perspective

A probability is useful only when its meaning is understood. OmniPhenoR
0.2.0 keeps probabilities alongside hard predictions so analysts can
inspect uncertainty, calibration, threshold sensitivity, and failure
modes rather than treating every model decision as equally certain.

## References

Gal, Yarin, and Zoubin Ghahramani. 2016. “Dropout as a Bayesian
Approximation: Representing Model Uncertainty in Deep Learning.”
*Proceedings of the 33rd International Conference on Machine Learning*,
Proceedings of machine learning research, vol. 48: 1050–59.

Guo, Chuan, Geoff Pleiss, Yu Sun, and Kilian Q. Weinberger. 2017. “On
Calibration of Modern Neural Networks.” *Proceedings of the 34th
International Conference on Machine Learning*, Proceedings of machine
learning research, vol. 70: 1321–30.
