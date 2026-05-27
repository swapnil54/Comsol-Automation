# COMSOL Multiphysics Automation with Grok AI

[![Python](https://img.shields.io/badge/Python-3.8+-3776AB.svg?logo=python&logoColor=white)](https://www.python.org/)
[![COMSOL](https://img.shields.io/badge/COMSOL-Multiphysics-0076A8.svg)](https://www.comsol.com/)
[![MPh](https://img.shields.io/badge/MPh-Automation-FF6F00.svg)](https://mph.readthedocs.io/)
[![Grok AI](https://img.shields.io/badge/Grok_AI-Assisted-000000.svg)](https://grok.x.ai/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

A Python-based automation framework for running **parallel COMSOL Multiphysics simulations** with configurable parameter sweeps. Built with the help of **Grok AI** to streamline large-scale photonic device optimization workflows.

This tool automates the process of loading COMSOL models, sweeping refractive index parameters across user-defined wavelength ranges, solving studies in parallel across multiple COMSOL server instances, and saving results — all without manual GUI interaction.

---

## 🚀 Key Features

* **Parallel Multi-Client Execution**: Spawns up to 5 independent COMSOL client instances simultaneously using Python's `multiprocessing`, dramatically reducing total sweep time.
* **Automated Parameter Sweeps**: Configurable sweep over refractive index (`n_ana`) with per-point wavelength ranges (`wl_start`, `wl_end`).
* **Smart Resume (v2)**: Version 2 detects previously completed simulations and skips them, enabling crash-safe, resumable batch runs.
* **Batch Orchestration**: Includes a Windows batch script to chain multiple optimization campaigns (e.g., varying metal thickness) sequentially with auto-shutdown.
* **Clean Logging**: Real-time console output with progress tracking, timing, and emoji status indicators (✅/❌).

---

## 📁 Repository Structure

```text
Comsol-Automation-using-Grok/
├── README.md                          # This file
├── LICENSE                            # MIT License
├── .gitignore                         # Python & COMSOL ignore rules
│
├── scripts/
│   ├── version_1.py                   # Basic parallel sweep automation
│   ├── version_2.py                   # Enhanced version with resume capability
│   └── run_all_simulations.bat        # Batch runner for sequential campaigns
│
└── docs/
    └── CHANGELOG.md                   # Version history & differences
```

---

## 📋 Prerequisites

| Requirement | Details |
|-------------|---------|
| **Python** | 3.8 or higher |
| **COMSOL Multiphysics** | Licensed installation with command-line server support |
| **MPh** | Python package for COMSOL automation (`pip install mph`) |
| **OS** | Windows (batch scripts use Windows-specific paths) |

---

## ⚡ Quick Start

### 1. Install dependencies

```bash
pip install mph
```

### 2. Configure your sweep

Edit the `sweep_config` list in `scripts/version_1.py` or `scripts/version_2.py`:

```python
sweep_config = [
    (1.28, 0.55, 0.65),   # (n_ana, wl_start, wl_end)
    (1.29, 0.55, 0.65),
    # ... add your parameter points
]
```

### 3. Update file paths

Set `BASE_MODEL` and `RESULTS_DIR` to point to your local COMSOL model and output directory:

```python
BASE_MODEL  = r'path\to\your\model.mph'
RESULTS_DIR = r'path\to\your\Results'
```

### 4. Run

```bash
# Run a single sweep campaign
python scripts/version_1.py

# Or use v2 with resume support
python scripts/version_2.py

# Or chain multiple campaigns
scripts\run_all_simulations.bat
```

---

## 🔄 Version Comparison

| Feature | Version 1 | Version 2 |
|---------|-----------|-----------|
| Parallel execution | ✅ 5 clients | ✅ 5 clients |
| Parameter sweep | ✅ Full config | ✅ Full config |
| Resume after crash | ❌ Restarts all | ✅ Skips completed |
| Progress detection | ❌ None | ✅ Scans results folder |
| Dynamic batch sizing | ❌ Fixed split | ✅ Adapts to remaining |

---

## 🛠️ How It Works

```
┌─────────────────┐     ┌──────────────┐     ┌──────────────────┐
│  Sweep Config    │────▶│  Split into  │────▶│  5 Parallel      │
│  (15 RI points)  │     │  5 batches   │     │  COMSOL Clients  │
└─────────────────┘     └──────────────┘     └──────┬───────────┘
                                                     │
                              ┌───────────────────────┘
                              ▼
                    ┌──────────────────┐
                    │  For each point: │
                    │  1. Load model   │
                    │  2. Set params   │
                    │  3. Solve study  │
                    │  4. Save .mph    │
                    │  5. Clear memory │
                    └──────────────────┘
```

Each client runs on a separate COMSOL server port (`2036`, `2046`, `2056`, ...) with 2 CPU cores, designed for machines like Ryzen 5 5500U with 16 GB RAM.

---

## 📌 Notes

- The scripts use `mph.start(cores=2, port=...)` — adjust `cores` based on your CPU.
- The batch file (`run_all_simulations.bat`) includes a `shutdown /s /t 30` command at the end. **Remove or comment this line** if you don't want your PC to shut down after completion.
- Paths in the scripts are configured for the original author's setup. **You must update them** before running.

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).

---

## 🤝 Acknowledgments

- **[Grok AI](https://grok.x.ai/)** — AI assistant used during development of these automation scripts.
- **[MPh](https://mph.readthedocs.io/)** — The Python package that makes COMSOL automation possible.
- **[COMSOL Multiphysics](https://www.comsol.com/)** — The simulation platform powering the physics.