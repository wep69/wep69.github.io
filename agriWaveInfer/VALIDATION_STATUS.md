# Validation status — agriWaveInfer 1.0.0

**Release state: SOURCE-FROZEN / RUNTIME-PENDING**

## Passed in the construction environment

- public API freeze/reconciliation;
- source-level export/definition/Rd reconciliation;
- metadata reconciliation across DESCRIPTION, CITATION.cff, CodeMeta and
  package citation;
- bibliography BibTeX/RIS DOI-set reconciliation;
- synthetic-data SHA-256 verification;
- frozen numerical/golden reference reconciliation;
- static delimiter and provisional-marker scans;
- deterministic ZIP/TAR.GZ round-trip verification.

## Not executed in this environment

- package load under R;
- `testthat`;
- optional-backend differential tests;
- truth-known recovery through the actual wavelet backend;
- vignette rendering;
- reproducibility-bundle runtime smoke tests;
- exact-tarball clean-library installation;
- `R CMD build`;
- `R CMD check --as-cran`.

These remain `NOT_RUN`, not `PASS`. Runtime-certified release status
must only be assigned after executing them on the exact final archive.
