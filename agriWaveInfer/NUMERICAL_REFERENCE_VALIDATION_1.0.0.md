# Numerical reference validation — agriWaveInfer 1.0.0

Version 1.0.0 inherits the frozen independent numerical references
developed through 0.9.0. The 1.0 release gate verifies their files and
hashes but does not relabel source-level mathematical/reference checks
as executed R runtime tests.

# Numerical and frozen-reference validation — agriWaveInfer 1.0.0

Version 1.0.0 introduces no new numerical wavelet estimator. Its
numerical obligation is therefore to preserve and freeze the validated
reference artifacts inherited from 0.3.0–0.8.0 while adding
publication-layer invariants.

## Frozen artifacts

`GOLDEN_MANIFEST_1.0.0.json` contains SHA-256 hashes for **14**
reference JSON/data CSV artifacts. Any change in these files after
freeze is detectable.

## Inherited numerical references

- Driver-domain AWC/PoSC and gain-loss values from 0.4.0.
- Phase/lag and nested predictive-precedence references from 0.5.0.
- Network truth-known lag and negative-control references from 0.7.0.
- Spatial truth-known driver/lag recovery references from 0.8.0.

## 1.0.0 publication-layer invariants

1.  Workflow orchestration must not change the numerical definition of
    an underlying estimator.
2.  `strict = FALSE` may record a failed step but must not relabel the
    failure as success.
3.  Reports must retain the causal-effect safeguard.
4.  Reproducibility bundles must contain the serialized object,
    provenance, session information, report, and manifest.
5.  Publication checks are source-level checks and never certify runtime
    execution.
6.  Metadata version must reconcile across DESCRIPTION, CITATION.cff and
    CodeMeta.

Runtime execution of these invariants remains `NOT_RUN` in the current
environment because R is unavailable.

## Deterministic data regeneration at 1.0.0 freeze

All six Python generators in `data-raw/` were rerun in an isolated copy
of the 1.0.0 source tree using Python 3.13.5, NumPy 2.3.5, and pandas
2.2.3. All **10 generated CSV files** matched the frozen source
byte-for-byte by SHA-256. This verifies deterministic regeneration of
the packaged synthetic/demo data in the construction environment.

This evidence does not substitute for R runtime validation of the
statistical functions.
