# Predictive Direction without Causal Overstatement

## Two different questions

Phase precedence asks which series occurs earlier within a coherent
oscillatory relationship. Granger-style predictive precedence asks
whether lagged values of one series improve prediction of another under
a specified autoregressive model (Granger 1969). They are related
concepts, but neither alone identifies an intervention-based causal
effect.

## Phase directionality

``` r

d <- awi_directionality(
  wtc_result,
  period_range = c(2, 7),
  phase_spec = awi_phase_spec(min_resultant_length = 0.6)
)
```

This output is labeled `phase_directional_association`.

## Experimental band-limited predictive precedence

``` r

g <- awi_band_granger(
  first = climate_driver,
  second = crop_response,
  period_range = c(24, 84),
  lag_order = 2
)
```

Version 0.5.0 uses deterministic zero-phase Fourier band filtering
followed by nested autoregressive comparisons. This diagnostic is
intentionally marked experimental because filtering, lag order, omitted
variables and model adequacy can change the result.

## Concordance, not causality

``` r

pd <- awi_predictive_direction(
  wtc_result,
  period_range = c(24, 84),
  predictive_test = TRUE,
  lag_order = 2
)
```

Possible outputs distinguish concordant phase and predictive precedence,
discordance, bidirectional predictive structure or inconclusive phase.
The object always contains `causal_effect = FALSE`.

## Recommended reporting

Report the period band, time window, phase concentration, principal lag,
lag order, preprocessing, F statistics, p-values and whether phase and
predictive precedence agree. Use terms such as “phase precedence”,
“predictive direction” or “directional predictability” rather than
“causal effect” unless a separate causal identification design justifies
that language.

Granger, C. W. J. 1969. “Investigating Causal Relations by Econometric
Models and Cross-Spectral Methods.” *Econometrica* 37 (3): 424–38.
<https://doi.org/10.2307/1912791>.
