# Changelog

All notable changes to the COMSOL Automation scripts are documented here.

---

## Version 2 — Enhanced with Resume Support

**Improvements over Version 1:**

- **Crash-Safe Resume**: Automatically scans the `RESULTS_DIR` for previously completed `.mph` files and skips them, so interrupted runs can resume without recomputing.
- **Dynamic Batch Sizing**: Splits only the *remaining* simulations across clients, rather than the full config, improving load balance after partial runs.
- **Early Exit**: If all simulations are already complete, the script exits immediately with a success message instead of launching COMSOL clients.
- **Adapted Client Count**: The number of active clients adjusts to the number of remaining tasks (e.g., if only 3 simulations remain, only 3 clients are spawned instead of 5).

---

## Version 1 — Basic Parallel Automation

**Features:**

- Spawns 5 parallel COMSOL client instances using Python `multiprocessing`.
- Each client loads the base `.mph` model, sets parameters (`n_ana`, `wl_start`, `wl_end`), solves Study 1, and saves the result.
- Fixed batch splitting across all 15 sweep configurations.
- Real-time progress logging with timing per simulation.
- Designed for Ryzen 5 5500U + 16 GB RAM (2 cores per client).

---

## Batch Runner — `run_all_simulations.bat`

A Windows batch script to chain multiple optimization campaigns (e.g., metal 35 nm → 40 nm → 45 nm → 55 nm → 60 nm) sequentially. Includes an optional auto-shutdown after all campaigns complete.
