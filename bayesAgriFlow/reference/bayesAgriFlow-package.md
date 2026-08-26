# Unified Bayesian Workflows for Agricultural Experimental Designs

The bayesAgriFlow package provides a design-aware and teaching-oriented
API for Bayesian analysis of agricultural and biological experiments.
Version 1.0.0 consolidates classical randomized designs, quantitative
regression, ANCOVA, posterior optima, mixed and repeated-measures
models, split-plot families, non-Gaussian GLMMs, nonlinear and flexible
models, spatial and G x E models, multivariate and imperfect-observation
workflows, model uncertainty, projection predictive selection, and
Bayesian decision analysis.

## Details

The public API separates design specification, prior specification,
computation, posterior fitting, diagnostics, posterior summaries,
evidence, practical relevance, response-scale prediction, predictive
model comparison, model averaging, decision analysis, and reporting.
Complex models are optional and may be fitted through brms using
cmdstanr or rstan; PyMC/PyMC-BART is an optional Python backend for
selected workflows. Bayes-factor evidence is kept distinct from effect
magnitude, predictive performance, and decision utility.

## Author

Walter Esfrain Pereira and Magali Haidee Pereira Martinez.
