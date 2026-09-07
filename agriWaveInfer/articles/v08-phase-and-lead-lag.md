# Phase and Multiscale Lead-Lag

## Why phase needs its own inferential layer

Wavelet coherence measures localized association, while phase describes
relative timing. A coherent phase pattern may indicate that one series
precedes another at selected periods, but phase is circular and should
not be equated with causality. The interpretation of wavelet phase
arrows follows a principal-branch convention and can become ambiguous
near anti-phase (Grinsted et al. 2004; Funashima 2017).

`agriWaveInfer` therefore separates coherence, phase masking, lag
conversion and band summarization.

## Scientific contract

``` r

ps <- awi_phase_spec(
  require_significance = TRUE,
  min_coherence = 0.4,
  mask_coi = TRUE,
  weighting = "coherence",
  min_resultant_length = 0.5
)
```

The default policy requires significant cells, excludes the cone of
influence and refuses to force a band-level direction when circular
concentration is weak.

## From phase to lag

For phase angle $`\phi(t,s)`$ and equivalent period $`P(s)`$, version
0.5.0 reports the principal signed lag

``` math
\tau(t,s)=\frac{\phi(t,s)}{2\pi}P(s).
```

Positive $`\tau`$ means the first stored series phase-leads the second.
The principal interval is $`[-P/2,P/2]`$, so the result remains
cycle-ambiguous.

``` r

w <- awi_wtc(driver, response, significance = TRUE, nrands = 199)
p <- awi_phase(w, ps)
l <- awi_phase_lag(p)
map <- awi_lead_map(w, ps)
```

## Band summaries use circular statistics

Arithmetic averaging of angles is inappropriate.
[`awi_lag_band()`](https://wep69.github.io/agriWaveInfer/reference/awi_phase.md)
uses the weighted complex resultant to obtain circular mean phase and
resultant length, and separately reports a weighted median of cell-level
principal lags.

``` r

b <- awi_lag_band(w, period_range = c(2, 7), phase_spec = ps)
b$resultant_length
b$weighted_median_lag
b$first_lead_persistence
```

A high resultant length indicates a concentrated phase pattern. It does
not establish a causal effect.

## Agronomic example

A cereal-yield analysis may show coherent ENSO-yield structure at
2-7-year scales. If ENSO consistently phase-leads yield outside the COI,
the result supports a scale-specific prediction window. Recent
crop-climate work has used phase-leading structure in significant
coherent bands for seasonal-to-annual predictability, while still
requiring robustness checks (Sankaran et al. 2026).

## What not to report

Do not write “ENSO caused yield changes because ENSO led yield in the
wavelet phase map.” A defensible statement is that ENSO showed
persistent phase precedence in a specified coherent band, with the
estimated principal lag and circular concentration reported explicitly.

Funashima, Yoshito. 2017. “Time-Varying Leads and Lags Across
Frequencies Using a Continuous Wavelet Transform Approach.” *Economic
Modelling* 60: 24–28. <https://doi.org/10.1016/j.econmod.2016.08.024>.

Grinsted, A., J. C. Moore, and S. Jevrejeva. 2004. “Application of the
Cross Wavelet Transform and Wavelet Coherence to Geophysical Time
Series.” *Nonlinear Processes in Geophysics* 11: 561–66.
<https://doi.org/10.5194/npg-11-561-2004>.

Sankaran, Adarsh, Fathima Fazal, Aleena Johnson, Venkataramana Sridhar,
Divyasree Prasad, and Aakhila Fathima Abdul Rasheed. 2026.
“Wavelet-Based Assessment of Climate Oscillation and Meteorological
Influences on Cereal Yield Variability Across India’s Agro-Climatic
Zones.” *Theoretical and Applied Climatology* 157: 147.
<https://doi.org/10.1007/s00704-026-06085-2>.
