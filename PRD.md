# PRD.md:  "The Why"; Product requirements, benchmark metadata table, high-level requirements, etc.

Product Requirements Document

"The Why"; Product requirements, benchmark metadata table, high-level requirements, etc. Defines the benchmark goal, validation objective, test name, benchmark number, category, and high-level success criteria.

## Benchmark Matrix Document Metadata (via benchmark_specification.json)

This PRD.md section is populated from benchmark_specification.json, which is the structured source of benchmark-specific product requirements.

## Workload Number
401

## Workload Name
System Config Verification

## Execution Summary (Run and Measure)
Run nvidia-smi, nvidia-smi -q, nvcc --version, dpkg-query, and /dev/nvidia* permission checks, then compare Driver Version, CUDA Version (major), VBIOS Version, required packages, and device-node access to yaml expectations, to measure CUDA stack compliance

## Main Goal
Analyze NVIDIA CUDA software stack consistency against yaml expectations

## Validation Objective
Validates NVIDIA driver, CUDA major version, VBIOS presence, listed dpkg packages, and /dev/nvidia* device-node permissions. Does not run DCGM

## Workload Category
System Validation & Reliability

## Validation Requirement

The benchmark must include an automated SQLite-integrated validation layer that verifies persisted results from `results/benchmark.db`. Validation must confirm:

1. The benchmark run completed successfully with no tool errors.
2. Required samples and aggregate metrics were persisted for every swept shape.
3. Metrics are finite and physically sensible (positive, within plausible bounds).
4. Measured values satisfy configured thresholds when the workload defines pass/fail gates.
5. The benchmark fails validation when required data is missing, invalid, or outside bounds.

## Non-Functional Requirements

| Requirement | Target |
|---|---|
| Automation | Runs to completion without manual intervention after `bash run_benchmark.sh` |
| Idempotency | Re-running `run_benchmark.sh` appends a new run; never corrupts existing rows |
| Persistence | All metrics survive script exit; `results/benchmark.db` is the durable record |
