# Results: 401 System Config Verification

**Main goal:** Analyze NVIDIA CUDA software stack consistency against yaml expectations  
**Tools:** Run nvidia-smi, nvidia-smi -q, nvcc --version, and dpkg-query, then check /dev/nvidia* permissions

Reference results measured by the maintainer on the system shown in each section (generator TEMPLATE_00_141 · recorded 2026-11-14). Your own runs write `results/RESULTS_ACTUAL.md` in the same format.

> Smoke runs use deliberately small sizes. They check that the workload installs and runs end to end; their numbers are not performance results. Baseline and extended are.

<!-- profile:smoke -->
## smoke

- **System:** 1× NVIDIA H100 PCIe (81559 MiB) · Intel(R) Xeon(R) Platinum 8352Y CPU @ 2.20GHz · Ubuntu 26.04.1 LTS · kernel 6.8.0-138-generic · driver 595.91.07
- **Run:** 2026-11-14T10:45:52Z · status **PASS** (exit 0)
- **Time (mm:ss):** setup 05:39 · benchmark 00:02 · whole run 05:53
- **Source:** run 401_20261114_104552
- **Command:** `bash run_benchmark.sh --smoke --kernel-version 7.0.0 --required-packages cuda-toolkit,nvidia-smi --permissions-check true --nvidia-driver-version 580 --cuda-version 13.3`

| # | Metric | Result |
|---|---|---|
| 1 | CUDA pkg mismatches (cuda_package_version_mismatches_count) | 0.0 |
| 2 | Kernel module/driver version mismatches (nvidia_kernel_module_driver_version_mismatches_count) | 0.0 |
| 3 | Driver version compliance percent (driver_compliance_percent) | 100.0 |
| 4 | VBIOS string present (vbios_string_present) | 1.0 |
| 5 | Device permission error count (required_library_path_device_permission_errors) | 0.0 |
<!-- /profile:smoke -->
