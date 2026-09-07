# Summary

`agriWaveInfer` is an R package for multiscale statistical inference in
agroclimatic and environmental time series. It provides a consistent
workflow for continuous and cross-wavelet transforms, wavelet coherence,
partial and multiple wavelet coherence, null-model and field-aware
significance procedures, driver screening and parsimonious selection,
phase-based lead-lag summaries, phenology-aware interpretation,
drought-propagation chains, time-frequency networks, and spatial
mapping. Rather than treating a wavelet scalogram as the endpoint of
analysis, the package records the scientific domain, conditioning
variables, null model, cone-of-influence policy, numerical diagnostics,
driver-selection rules, phase conventions, and provenance needed to
interpret a result reproducibly.

# Statement of need

Agricultural and environmental systems combine processes that act at
different temporal scales. Teleconnections can influence rainfall and
temperature, soil moisture can mediate meteorological anomalies,
vegetation responds after additional delays, and crop yield integrates
these processes over phenologically meaningful windows. Pairwise
correlation or a single global spectrum can obscure this scale
dependence. Existing R software provides important transform and
coherence algorithms, but researchers frequently combine packages and
custom scripts for partial conditioning, multiple-driver coherence,
surrogate inference, multiple-testing control, driver selection,
phase-to-lag conversion, spatial summaries, and network construction.
`agriWaveInfer` addresses this workflow gap with explicit scientific
contracts and auditable outputs while building on existing transform
engines rather than replacing them.

# State of the field

The package deliberately does not claim novelty for continuous wavelet
transforms, bivariate coherence, partial wavelet coherence, or multiple
wavelet coherence themselves. Packages such as `biwavelet`,
`WaveletComp`, and `vectorwavelet` already provide important
implementations. The scholarly contribution of `agriWaveInfer` is the
integration of conditional and multivariate coherence with numerical
conditioning diagnostics, multiple null models, field-aware
false-discovery or cluster inference, driver attribution, phase-aware
lead-lag interpretation, phenological stratification, propagation
summaries, scale-specific networks, and spatial workflows under one
reproducible data and metadata model. This design favors differential
validation against existing implementations and explicit reporting of
methodological choices over reinvention of established transforms.

# Software design

Small scientific policy objects use S7 classes and validators, including
wavelet, conditioning, null-model, driver-selection, phase, phenology,
propagation, network, spatial, and workflow specifications. Large
numerical results remain S3 objects to preserve interoperability with
standard R matrices, data frames, `igraph`, `terra`, and `stars`.
Optional transform and geospatial backends remain in `Suggests`, so
installation of the core package does not silently install heavy
compiled dependencies. Version 1.0.0 consolidates a workflow layer that
orchestrates existing estimators without redefining their statistics, a
report generator, reproducibility bundles containing serialized results
and provenance, and a source-tree publication checker.

The package distinguishes association, temporal precedence, and
causality throughout the API. Phase lead, band-limited predictive
precedence, dominant spatial drivers, propagation chains, and directed
wavelet-network edges are not labeled as causal effects. Numerical
safeguards include local matrix rank and condition diagnostics for
multiple wavelet coherence, explicit regularization records,
cone-of-influence masking, circular statistics for phase, and
memory-aware chunk planning for spatial analyses.

# Research impact statement

Version 1.0.0 is the consolidated scientific software release of the
0.1.0–0.9.0 development series. It contains deterministic truth-known
datasets for phase lags, driver selection, phenology and propagation,
network topology, and spatial driver/lag recovery, together with
differential, numerical, property, metamorphic, and invariant test
scaffolds. The software is intended for agronomists, climatologists,
environmental scientists, and quantitative researchers studying
scale-dependent relationships among teleconnections, meteorological
variables, soil water, vegetation indices, phenology, and yield. Runtime
certification on the exact release archive, public repository history,
external adoption evidence, and release-specific archival identifiers
remain requirements before a journal submission can claim realized
community impact.

# AI usage disclosure

Generative AI tools were used during software development to assist with
code scaffolding, refactoring, test design, documentation drafting,
metadata reconciliation, and preparation of this paper scaffold. The
human authors remain responsible for scientific design decisions,
verification of references and metadata, execution and review of runtime
validation, licensing, interpretation, and all claims in a submitted
version. The exact tools/models and the final scope of assistance must
be updated by the authors immediately before submission to reflect the
public development record and the software state being reviewed.

# Acknowledgements

Funding, institutional acknowledgements, and project identifiers should
be added by the authors before submission.

# References
