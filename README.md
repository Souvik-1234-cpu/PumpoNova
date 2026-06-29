# Pumponova

Independent re-build that develops the PumpSmart work further. PumpSmart is **memory and reference only** (see docs/).
Route: Part E foundational re-analysis, starting at M1. Governed by the v2.0 instruction set — Section 2 (rigor discipline) is binding.

## Layout
- `config/` — central config (asset constants, DEVICE, paths)
- `scripts/` — `thread_a_segmentation` (primary, M1 re-segmentation), `thread_b_leakage_diag` (parallel, frozen prototype only), `utils` (shared)
- `data/` — raw / interim / processed (gitignored contents)
- `outputs/` — per-thread outputs + artifacts (gitignored contents)
- `reports/` — per-thread .md reports + `session_logs` (work log, paste-text)
- `plots/` — per-thread physics visualisations (gitignored contents)
- `contributions/` — TEAM candidate inputs by task type, each with `history/`; `verified/` holds only cross-checked items
- `docs/` — reference documents (memory, not inheritance)
- `frozen_reference/` — pointer to the untouched PumpSmart benchmark

## Two truth-numbers (never conflate)
- Offline held-out sequence macro F1 = 0.9529 (capability)
- Live single-window classification ~52% (serving-gap lower bound)
- Real-world F1 expectation 0.65-0.85 (C-26 disclaimer)
