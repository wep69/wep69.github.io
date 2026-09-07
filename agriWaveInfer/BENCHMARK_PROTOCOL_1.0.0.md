# Benchmark protocol — agriWaveInfer 1.0.0

This protocol consolidates the benchmark targets defined during
0.3.0–0.9.0. Runtime benchmark values are not fabricated in the
source-construction environment. Execute the protocol on the exact
release tarball and preserve machine, R, backend versions, seeds,
dimensions, elapsed time, peak memory and result checksums.

# Benchmark protocol — agriWaveInfer 1.0.0

## Scientific recovery

Retain all truth-known batteries from 0.3.0–0.8.0: Type-I field
behavior, driver recovery, phase lag, propagation, network topology, and
spatial driver/lag recovery.

## Workflow overhead

Measure
[`awi_workflow()`](https://wep69.github.io/agriWaveInfer/reference/awi_workflow.md)
overhead relative to direct calls with identical specifications. The
orchestration layer should add negligible numerical overhead beyond
object retention/reporting.

## Reproducibility bundle

Measure bundle size with and without intermediate fields and verify that
serialized objects reload, manifests enumerate every file, and explicit
seeds reproduce stochastic analyses.

## Scalability

Repeat spatial benchmark grids with chunk sizes 1, 8, 32, and 64 and
verify scientific invariance within floating-point tolerance.

All benchmark claims require real R runtime execution and are `NOT_RUN`
in the source-construction environment.
