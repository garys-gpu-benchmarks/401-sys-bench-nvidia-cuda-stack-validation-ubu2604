# SPEC.md: "The Human How"; Exact technical requirements, environment setup, implementation details, etc.

## Execution Description

Compares the live NVIDIA CUDA stack against yaml expectations and emits one CSV row. kernel_version: expected uname -r prefix. nvidia_driver_version: substring of nvidia-smi Driver Version. cuda_version: CUDA major from nvidia-smi, falling back to nvcc --version. Sweep dimensions: kernel_version, nvidia_driver_version, cuda_version, vbios_inforom_versions, required_packages, permissions_check, output_format.

## Parameters

| Parameter | CLI Flag | Tested Values | Default | Description |
| --- | --- | --- | --- | --- |
| kernel_version | `--kernel-version` | smoke=7.0.0, baseline=7.0.0, extended=7.0.0 | 7.0.0 | From Parameter list; see Execution Description With Parameters. |
| nvidia_driver_version | `--nvidia-driver-version` | smoke=580, baseline=580, extended=580 | 580 | From Parameter list; see Execution Description With Parameters. |
| cuda_version | `--cuda-version` | smoke=13.3, baseline=13.3, extended=13.3 | 13.3 | From Parameter list; see Execution Description With Parameters. |
| vbios_inforom_versions | `--vbios-inforom-versions` | smoke=present, baseline=present, extended=present | present | From Parameter list; see Execution Description With Parameters. |
| required_packages | `--required-packages` | smoke=cuda-toolkit,nvidia-smi, baseline=cuda-toolkit,nvidia-smi, extended=cuda-toolkit,nvidia-smi | cuda-toolkit,nvidia-smi | From Parameter list; see Execution Description With Parameters. |
| permissions_check | `--permissions-check` | smoke=true, baseline=true, extended=true | true | From Parameter list; see Execution Description With Parameters. |
| output_format | `--output-format` | smoke=csv, baseline=csv, extended=csv | csv | From Parameter list; see Execution Description With Parameters. |

## Invocation

```bash
Run nvidia-smi, nvidia-smi -q, nvcc --version, and dpkg-query, then check /dev/nvidia* permissions
```

## Raw Output Format

CSV with one row per CUDA stack check. A single row is duplicated to two samples

sample_index,status,cuda_package_version_mismatches_count,cuda_major_version_mismatch_count,nvidia_kernel_module_driver_version_mismatches_count,vbios_string_present,driver_compliance_percent,required_library_path_device_permission_errors,error_message
0,ok,0,0,0,1,100,0,

## Metrics

- **#1: CUDA pkg mismatches** — stored as `cuda_package_version_mismatches_count`.
- **#2: Kernel module/driver version mismatches** — stored as `nvidia_kernel_module_driver_version_mismatches_count`.
- **#3: Driver version compliance percent** — stored as `driver_compliance_percent`.
- **#4: VBIOS string present** — stored as `vbios_string_present`.
- **#5: Device permission error count** — stored as `required_library_path_device_permission_errors`.

## Framework

Compares the live NVIDIA CUDA stack against yaml expectations and emits one CSV row. kernel_version: expected uname -r prefix. nvidia_driver_version: substring of nvidia-smi Driver Version.

## Installation and Execution Summary

Run nvidia-smi, nvidia-smi -q, nvcc --version, dpkg-query, and /dev/nvidia* permission checks, then compare Driver Version, CUDA Version (major), VBIOS Version, required packages, and device-node access to yaml expectations, to measure CUDA stack compliance

## Platform Portability

- **AMD (primary):** ```bash
Run nvidia-smi, nvidia-smi -q, nvcc --version, and dpkg-query, then check /dev/nvidia* permissions
```
- **NVIDIA:** Native NVIDIA CUDA workload. Execute on the stated Ubuntu release with the host NVIDIA driver and CUDA userspace. ROCm porting notes do not apply.

## Model Context Protocols

- **Active:** None

## Execution-Loop Validation Contract

EXECUTION CHAIN: `run_benchmark.sh` ➔ raw output ➔ `scripts/parse_results.py` ➔ `results/benchmark.db` ➔ `scripts/validate_results.py`

This benchmark uses a lightweight, SQLite-integrated execution loop for result validation. All validation is performed by `scripts/validate_results.py`.

### Validation script usage

```bash
export BENCHMARK_PYTHON=/usr/bin/python3.13  # optional; select the installed interpreter

# After a live run:
".venv/bin/python" scripts/validate_results.py --db results/benchmark.db

# CI / no-GPU path (seeds fixture and validates it):
".venv/bin/python" scripts/validate_results.py --seed-fixture --quiet

# Override DB path via environment variable:
BENCHMARK_DB=tests/fixtures/benchmark.db \
  ".venv/bin/python" scripts/validate_results.py
```

### Run artifact contract

CSV with one row per CUDA stack check. A single row is duplicated to two samples

sample_index,status,cuda_package_version_mismatches_count,cuda_major_version_mismatch_count,nvidia_kernel_module_driver_version_mismatches_count,vbios_string_present,driver_compliance_percent,required_library_path_device_permission_errors,error_message
0,ok,0,0,0,1,100,0,

```bash
bash run_benchmark.sh --help
bash run_benchmark.sh --profile smoke --validate
bash run_benchmark.sh --profile baseline --validate
bash run_benchmark.sh --profile extended --validate
```
`run_benchmark.sh --help` prints usage and exits. The harness calls `scripts/ensure_setup.sh` when `.setup_state` is absent.

### Required integrity checks (built into `validate_results.py`)

1. Latest run exists and `runs.status = 'ok'`.
2. `run.error_message` is NULL.
3. `started_at` and `finished_at` are valid ISO-8601 UTC strings.
4. All required aggregate metrics in `runs` are non-NULL and finite.
5. All required aggregate metrics are physically sensible (positive values). Compares the live NVIDIA CUDA stack against yaml expectations and emits one CSV row. kernel_version: expected uname -r prefix. nvidia_driver_version: substring of nvidia-smi Driver Version.
6. At least 2 sample rows exist for the latest `run_id` (sweep coverage).
7. No sample has `status = 'error'`.
8. Compares the live NVIDIA CUDA stack against yaml expectations and emits one CSV row. kernel_version: expected uname -r prefix. nvidia_driver_version: substring of nvidia-smi Driver Version.

### Baseline / Threshold configuration (`config/benchmark_config.yaml`)

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

Threshold key suffixes encode comparison direction when `thresholds:` is present: `_min` → observed value must be ≥ threshold. `_max` → observed value must be ≤ threshold. Informational `baselines:` ranges are not pass/fail gates.
