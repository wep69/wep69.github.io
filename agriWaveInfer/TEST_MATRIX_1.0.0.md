# Test matrix — agriWaveInfer 1.0.0

| Layer | Examples | Source state | Runtime state |
|----|----|----|----|
| API contracts | export freeze, S7 validators, input errors | present | NOT_RUN here |
| Differential | CWT/XWT/WTC vs biwavelet; MWC oracle | present | NOT_RUN here |
| Property | coherence bounds, permutation invariance | present | NOT_RUN here |
| Metamorphic | reorder predictors/controls, chunk invariance | present | NOT_RUN here |
| Domain invariants | phase sign, lag bounds, causal safeguard | present | NOT_RUN here |
| Null/field inference | surrogate invariants, FDR/cluster | present | NOT_RUN here |
| Driver selection | truth-known selection/stability | present | NOT_RUN here |
| Phenology/propagation | stage and lag recovery | present | NOT_RUN here |
| Networks | topology/direction/persistence | present | NOT_RUN here |
| Spatial | dominant driver, lag, chunk invariance | present | NOT_RUN here |
| Publication | workflow/report/bundle/check | present | NOT_RUN here |
| Release | metadata, API freeze, archive round-trip | present | source-audited |

Runtime evidence must update `VALIDATION_LEDGER_1.0.0.csv` before
certification.
