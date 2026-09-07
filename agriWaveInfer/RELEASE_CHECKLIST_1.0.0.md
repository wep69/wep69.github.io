# Release checklist — agriWaveInfer 1.0.0

## Source/API

Version set to 1.0.0.

No new methodological domain added.

Export set frozen and recorded.

S3 registrations reconciled with definitions.

Export aliases reconciled with Rd documentation.

Minimum R/S7 contract documented.

## Scientific integrity

Causal-effect safeguards retained.

Golden/reference artifacts consolidated.

Synthetic-data hashes verified.

Bibliographic DOI sets reconciled.

Optional backends remain capabilities rather than silent installs.

## Release engineering

DESCRIPTION/CITATION.cff/CodeMeta/inst/CITATION reconciled.

API lifecycle and freeze documents generated.

Deterministic source archives generated and round-trip verified.

External SHA-256 file generated.

## Runtime certification

R package load on exact release archive.

Full testthat suite.

Optional-backend differential tests.

Truth-known wavelet recovery.

All vignettes rendered.

Reproducibility-bundle runtime smoke.

Clean-library installation.

`R CMD build`.

`R CMD check --as-cran`.

Unchecked runtime items remain release-certification blockers, not
source-construction blockers.
