# Architecture — agriWaveInfer 1.0.0

## Design principle

`agriWaveInfer` separates scientific contracts, numerical engines,
inference policy, interpretation, and publication provenance.
Established transforms are reused through optional backends; the package
contribution is a coherent, auditable inference workflow rather than
reinvention of every wavelet algorithm.

## Object systems

Small scientific policy objects use S7: `AwiDesign`, `AwiWaveletSpec`,
`AwiConditionSpec`, `AwiNullSpec`, `AwiDriverSpec`, `AwiPhaseSpec`,
`AwiPhenologySpec`, `AwiPropagationSpec`, `AwiNetworkSpec`,
`AwiSpatialSpec`, and `AwiWorkflowSpec`. Large numerical results remain
S3 for interoperability and lower structural overhead.

## Layered flow

``` text
scientific data / design
        ↓
wavelet representation
        ↓
conditional + multivariate coherence
        ↓
null models + field inference
        ↓
driver attribution / selection
        ↓
phase + lead-lag / predictive precedence
        ↓
phenology + propagation
        ↓
time-frequency networks
        ↓
spatial inference / scalability
        ↓
workflow + report + reproducibility bundle
```

## Optional capability policy

`biwavelet`, `WaveletComp`, `vectorwavelet`, `igraph`, `terra`, `stars`,
and `digest` remain optional. The core must load without them.
[`awi_capabilities()`](https://wep69.github.io/agriWaveInfer/reference/awi_capabilities.md)
records installed capabilities and validation tier.

## Numerical safeguards

The architecture records cone-of-influence policy, local matrix
rank/condition diagnostics, regularization, surrogate/null
specification, multiple-testing strategy, circular phase concentration,
valid-domain coverage, spatial memory plans, and causal-effect
safeguards.

## Causal boundary

The package estimates multiscale association, conditional association,
temporal precedence, phase direction, spatial dominance, and network
structure. None of these quantities alone identifies a causal effect.
