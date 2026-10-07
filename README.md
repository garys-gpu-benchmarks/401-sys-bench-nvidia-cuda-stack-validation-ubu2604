# System Config Verification Benchmark

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![CI](https://img.shields.io/badge/CI-host--safe-green.svg)](.github/workflows/ci.yml)

Target: Ubuntu 26.04 · NVIDIA · see Hardware Requirements. This is a host benchmark, not a laptop `pip install` project.

## Quick Start

```bash
git clone https://github.com/garys-gpu-benchmarks/401-sys-bench-nvidia-cuda-stack-validation-ubu2604.git
cd 401-sys-bench-nvidia-cuda-stack-validation-ubu2604
sudo bash setup.sh --assume-yes
bash run_benchmark.sh --profile smoke --validate
```
Results are written to `results/benchmark.db` and `results/summary.json`.

This workload is executed on the validation host after the repository is copied there. `setup.sh` and `run_benchmark.sh` do not open an outbound SSH session.

Prerequisites: Ubuntu 26.04; NVIDIA; Python 3.14.4; root or sudo for `setup.sh`. Framework: Bash, SQLite, Python, PyYAML, CUDA Runtime, NVCC, nvidia-smi. This is a host benchmark, not a laptop `pip install` project.

```mermaid
flowchart LR
  setup.sh --> run_benchmark.sh --> parse_results.py --> results/benchmark.db
```

## 1. Overview

Compares the live NVIDIA CUDA stack against yaml expectations and emits one CSV row. kernel_version: expected uname -r prefix. nvidia_driver_version: substring of nvidia-smi Driver Version. cuda_version: CUDA major from nvidia-smi, falling back to nvcc --version. Sweep dimensions: kernel_version, nvidia_driver_version, cuda_version, vbios_inforom_versions, required_packages, permissions_check, output_format.

## 2. What It Validates

- Validates NVIDIA driver, CUDA major version, VBIOS presence, listed dpkg packages, and /dev/nvidia* device-node permissions. Does not run DCGM
- #1: CUDA pkg mismatches (cuda_package_version_mismatches_count); is present and physically sensible.
- #2: Kernel module/driver version mismatches (nvidia_kernel_module_driver_version_mismatches_count); is present and physically sensible.
- #3: Driver version compliance percent (driver_compliance_percent); is present and physically sensible.
- #4: VBIOS string present (vbios_string_present); is present and physically sensible.
- #5: Device permission error count (required_library_path_device_permission_errors) is present and physically sensible.

## 3. Metrics Captured

- **#1: CUDA pkg mismatches** — stored as `cuda_package_version_mismatches_count`.
- **#2: Kernel module/driver version mismatches** — stored as `nvidia_kernel_module_driver_version_mismatches_count`.
- **#3: Driver version compliance percent** — stored as `driver_compliance_percent`.
- **#4: VBIOS string present** — stored as `vbios_string_present`.
- **#5: Device permission error count** — stored as `required_library_path_device_permission_errors`.

## 4. Hardware Requirements

### Supported environment

- OS: Ubuntu 26.04
- GPU vendor: NVIDIA
- Framework family: Bash, SQLite, Python, PyYAML, CUDA Runtime, NVCC, nvidia-smi
- Python: Python 3.14.4

### Reference validation environment

The tables below describe the machine used to generate the reference results. They are not a requirement that every user buy that exact cloud instance.

### System

Compares the live NVIDIA CUDA stack against yaml expectations and emits one CSV row. kernel_version: expected uname -r prefix. nvidia_driver_version: substring of nvidia-smi Driver Version.

### GPU

Ubuntu 26.04 / NVIDIA / Bash, SQLite, Python, PyYAML, CUDA Runtime, NVCC, nvidia-smi

## 5. Software Requirements

| Component | Version |
|---|---|
| OS | Ubuntu 26.04 |
| Kernel | kernel 7.0.0 |
| Python | Python 3.14.4 |
| ROCm | CUDA 13.3 |
| rocBLAS | N/A - rocBLAS not used |

Compares the live NVIDIA CUDA stack against yaml expectations and emits one CSV row. kernel_version: expected uname -r prefix. nvidia_driver_version: substring of nvidia-smi Driver Version.

## 6. Installation

```bash
Run nvidia-smi, nvidia-smi -q, nvcc --version, and dpkg-query, then check /dev/nvidia* permissions
```

## 7. Running the Benchmark

```bash
Run nvidia-smi, nvidia-smi -q, nvcc --version, and dpkg-query, then check /dev/nvidia* permissions
```

**Validating results separately:**

```bash
export BENCHMARK_PYTHON=/usr/bin/python3.13  # optional
python3 -m venv .venv
source ".venv/bin/activate"
".venv/bin/python" scripts/validate_results.py
```

## 8. Output

### `results/benchmark.db` (SQLite)

CSV with one row per CUDA stack check. A single row is duplicated to two samples

sample_index,status,cuda_package_version_mismatches_count,cuda_major_version_mismatch_count,nvidia_kernel_module_driver_version_mismatches_count,vbios_string_present,driver_compliance_percent,required_library_path_device_permission_errors,error_message
0,ok,0,0,0,1,100,0,

```bash
Run nvidia-smi, nvidia-smi -q, nvcc --version, and dpkg-query, then check /dev/nvidia* permissions
```

### `results/summary.json`

Consolidated metrics from the most recent run — suitable for CI artifact upload or dashboard ingestion.

### `results/raw/<timestamp>.txt`

CSV with one row per CUDA stack check. A single row is duplicated to two samples

sample_index,status,cuda_package_version_mismatches_count,cuda_major_version_mismatch_count,nvidia_kernel_module_driver_version_mismatches_count,vbios_string_present,driver_compliance_percent,required_library_path_device_permission_errors,error_message
0,ok,0,0,0,1,100,0,

## 9. Baselines / Thresholds

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

## 10. Troubleshooting

**`setup.sh` missing collector**
Create cannot finish without `scripts/collect_workload.py`.

**`self_check` overlay rewritten**
Do not overwrite files listed in `results/overlay_lock.json`.

**Remote SSH drop during setup**
Reconnect and resume `bash setup.sh --assume-yes`. Do not wipe `.venv` or `.cache`.

## 11. NVIDIA H100 Coding Differences

Native NVIDIA CUDA workload. Execute on the stated Ubuntu release with the host NVIDIA driver and CUDA userspace. ROCm porting notes do not apply.

## Repository layout

```text
.
├── setup.sh
├── run_benchmark.sh
├── benchmark_specification.json
├── config/
├── scripts/
├── src/
├── tests/
├── docs/
├── results/
└── LICENSE
```
