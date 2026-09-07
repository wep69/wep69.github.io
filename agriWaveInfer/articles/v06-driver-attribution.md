# Driver attribution: AWC, PoSC and conditional support

## Why attribution needs more than a coherence plot

A wavelet-coherence field answers where two signals are locally
associated in time and scale. Driver attribution asks a harder question:
which candidate driver is most informative over a scientifically defined
domain, whether additional drivers materially improve a multivariate
representation, and whether a selected driver retains conditional
support after related predictors are controlled.

`agriWaveInfer` 0.4.0 therefore separates three layers:

1.  descriptive strength through Average Wavelet Coherence (AWC);
2.  inferential coverage through Percentage of Significant Coherence
    (PoSC);
3.  conditional support through improved partial wavelet coherence
    (PWC).

This separation is important because an increase in MWC or PoSC after
adding a predictor does not prove that the new predictor contributes
independently. Recent crop-climate work using a coherence elimination
framework makes this limitation explicit and points to PWC as a
complementary analysis (Sankaran et al. 2026). Hydroclimatic work has
likewise combined BWC, MWC and PWC to distinguish dominant
teleconnection drivers (Mohan et al. 2023).

## Explicit domain $`\Omega`$

Let $`R^2(t,s)`$ denote squared wavelet coherence. For a chosen
time-period domain $`\Omega`$,

``` math
\mathrm{AWC}=\frac{1}{A}\iint_{\Omega}R^2(t,s)\,dt\,ds,
```

where $`A`$ is the area of the valid domain. PoSC is

``` math
\mathrm{PoSC}=100\times\frac{\mathrm{Area}\{(t,s)\in\Omega:\text{significant}\}}{A}.
```

The package defaults to excluding the cone of influence and uses
discrete cell-area weighting. Equal-cell weighting remains available for
compatibility with analyses that summarize the lattice directly.
AWC/PASC-type summaries have been used to identify multivariate
environmental controls of soil organic matter (Zhao et al. 2018).

``` r

awc <- awi_awc(
  fit,
  period_range = c(24, 96),
  weighting = "area"
)

posc <- awi_posc(
  fit,
  period_range = c(24, 96),
  weighting = "area"
)
```

The returned object stores the estimate together with the numerator,
denominator, domain coverage, weighting and COI policy. Therefore a
scalar metric cannot be detached from the domain that produced it.

## Gain-loss is a parsimony rule, not a causal test

For baseline set $`B`$ and candidate set $`C`$,

``` math
\Delta\mathrm{PoSC}=\mathrm{PoSC}_{C}-\mathrm{PoSC}_{B}.
```

The recent cereal-yield framework retained additional predictors when
PoSC increased by at least five percentage points (Sankaran et al.
2026). `agriWaveInfer` exposes that rule but does not universalize it:

``` r

g <- awi_gain_loss(
  baseline = mwc_enso,
  candidate = mwc_enso_pdo,
  metric = "posc",
  min_gain = 5
)
```

The output uses **percentage points**, not relative percent change.

## Conditional support

Suppose the retained set is ENSO + PDO. The increase in joint MWC may
reflect shared information between ENSO and PDO. The optional
conditional layer evaluates each retained driver against the response
while controlling the remaining selected drivers:

``` math
R^2_{Y,\mathrm{ENSO}\mid\mathrm{PDO}}(t,s),
\qquad
R^2_{Y,\mathrm{PDO}\mid\mathrm{ENSO}}(t,s).
```

A selected driver can therefore have high contribution to a joint set
but weak conditional PoSC. That discrepancy is scientifically
informative rather than an error.

## Interpretation guardrail

Driver screening, MWC gain, PoSC and PWC quantify multiscale association
and conditional association. They do not identify a causal effect.
Attribution claims should remain proportional to the design, null model,
conditioning set, temporal resolution and external scientific knowledge.

## References

Mohan, Meera G., S. Fathima, S. Adarsh, et al. 2023. “Analyzing the
Streamflow Teleconnections of Greater Pampa Basin, Kerala, India Using
Wavelet Coherence.” *Physics and Chemistry of the Earth, Parts A/B/C*
131: 103446. <https://doi.org/10.1016/j.pce.2023.103446>.

Sankaran, Adarsh, Fathima Fazal, Aleena Johnson, Venkataramana Sridhar,
Divyasree Prasad, and Aakhila Fathima Abdul Rasheed. 2026.
“Wavelet-Based Assessment of Climate Oscillation and Meteorological
Influences on Cereal Yield Variability Across India’s Agro-Climatic
Zones.” *Theoretical and Applied Climatology* 157: 147.
<https://doi.org/10.1007/s00704-026-06085-2>.

Zhao, Ruiying, Asim Biswas, Yin Zhou, Yue Zhou, Zhou Shi, and Hongyi Li.
2018. “Identifying Localized and Scale-Specific Multivariate Controls of
Soil Organic Matter Variations Using Multiple Wavelet Coherence.”
*Science of the Total Environment* 643: 548–58.
<https://doi.org/10.1016/j.scitotenv.2018.06.210>.
