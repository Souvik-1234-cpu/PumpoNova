# Pumponova — Work Log

> Recorded decisions and running history for the Pumponova foundational re-analysis (Part E).
> PumpSmart is **memory and reference only**. Section 2 (rigor discipline) is binding on every step.
> This file is the top-of-project record required by E.8 step 3 — the three edges are settled here
> before any deep analysis begins.

---

## SESSION 1 — Foundation setup

**Date:** 2026-06-29
**Stage:** P2-0 → 4.2 → 4.2b → 4.3
**Repo:** github.com/Souvik-1234-cpu/PumpoNova
**Local:** C:\Users\user\Desktop\Pumponova_Project

### Completed before this point
- P2-0 freeze confirmed: PumpSmart M1–M12 prototype is fully working and preserved as the runnable
  benchmark (never edited).
- Two truth-numbers recorded and kept separate:
  - Offline held-out sequence macro F1 = **0.9529** (true capability).
  - Live single-window classification ~**52%** (serving-gap lower bound); detection 100%; p95 31 ms.
  - Real-world expectation band **F1 0.65–0.85** (C-26 disclaimer) — acknowledged.
- Fresh project opened (4.2): new folder, fresh `.venv`, locked stack (torch 2.6.0+cu126, RTX 4060
  verified `cuda True`), `.gitignore` committed.
- Project skeleton committed + pushed (4.2b): scripts / data / outputs / reports / plots /
  contributions / docs / frozen_reference, with per-thread `history/` and team contribution subfolders.

---

## THE THREE DEFINITIONAL EDGES (E.7 / Section 4.3) — SETTLED

> Settled in session 1, before deep analysis. These sharpen *how* the analysis is conducted without
> pre-deciding *what* it concludes.

### Edge 1 — The collapse-bar (what justifies collapsing the build)

A finding crosses the collapse-bar — i.e. is strong enough to justify restarting the route from the
ground — **only when all three hold:**

1. **Physically explained.** The finding is grounded in pump physics (affinity laws, NPSH/cavitation,
   thermal time constants, conservation, the real working signatures of this asset), not merely
   observed in a trace.
2. **Reproducible.** It appears across **more than one** discarded segment / dataset, not a single
   fragment. A one-off is logged as *candidate*, not collapse-triggering.
3. **Literature-authenticated** (Section 2.3). It matches a literature/physics signature on stated
   grounds, or its disagreement is itself explained physically.

Findings that are one-off, unexplained, or unliteratured are recorded as **candidate**, never as a
collapse trigger. The candidate triggers from E.7 (real transient signature contradicting the synthetic
profile; recurring break-cause mapping to an unmodelled fault; the 1s/50s usability rule having
discarded a whole regime or fault family; a measured envelope invalidating an M2–M4 calibration; an
anomaly deviating from literature normal behaviour on the 4 Principal Criteria) are all subject to the
three-part bar above.

### Edge 2 — The analysis vocabulary

Applied uniformly to every pattern (anomaly **and** normal sequence alike).

**(a) Break-cause categories** — descriptive buckets for *why a raw segment broke*. Each must get a
grounded mechanism before it is used (Section 2.2 — no name-only constructs):
- normal shutdown
- protective trip
- startup transient
- sensor dropout / logging failure
- genuine fault
- unknown (default until evidence assigns a cause)

These are NOT the 24-class fault taxonomy — they classify segment breaks, not fault types.

**(b) Deviation measure** — measured against the **raw physical traces, in real engineering units**
(bar, °C, m³/h, mm/s vibration, kW, RPM), **NOT** normalised / z-scored. The reference envelope is the
literature + physics normal-working envelope for this asset (110 kW / 7-stage / 40 bar / 450 m /
2980 RPM / 45 m³/h). Rationale: team scope is raw-sensor graph interpretation on physical traces;
deviations are judged on physical grounds, not against a model-internal normalisation.

**(c) Signature descriptors** — controlled vocabulary for *how* a trace deviates:
step · ramp · spike · oscillation · drift · dropout.

**(d) The 4 Principal Criteria (foundational core)** — every pattern characterised on all four,
contrasted against literature signatures / physics equations / validated transport phenomena:
1. **WHICH** sensor parameters are changing
2. **HOW MUCH** they change (magnitude vs the normal envelope, in real units)
3. **HOW** they change (the signature descriptor from (c))
4. **RATE** — how fast they change (rate-of-change where the physics is a rate, per Section 1.3)

### Edge 3 — One foundational pass or two

**Decision: TWO distinct investigations.**
- **Thread A — M1 raw re-segmentation — PRIMARY.** Re-segment the raw pre-clean data without
  discarding short/broken segments; explain every break in the Edge-2 vocabulary.
- **Thread B — leakage / saturation diagnostic — PARALLEL, on the frozen prototype ONLY.** Diagnose
  why prior confidence saturates (100% → 0.94) on clean data. Kept separate so findings don't
  contaminate each other.

Rationale: if Thread A changes the M1 foundation, the M2→M7 matrix that leaks may cease to exist in
its current form, making a standalone leakage hunt partly moot — but Thread B still runs in parallel
because it is the evidence that *justifies* re-founding at all (E.5). Governed by Section 2.4.

---

## RAW DATASET INVENTORY (Section 2.1 — stated; Section 2.5 — verify on load)

Files now present in `data/raw/` (9 CSVs), confirmed by inspection + cross-checked against the
official CIRA SACIP dataset description (Zenodo). Schema and clean/corrupt status established from
Excel inspection of two files plus the source documentation.

### Files
| Pump | Day1 | Day2 | Day3 |
|------|------|------|------|
| A    | clean | clean | clean |
| B    | clean | clean | clean |
| C    | clean | clean | **CORRUPTED — see below** |

### Real schema (11 columns, from inspection + source table)
`Timestamp` (DD-MM-YYYY hh:mm), then ten measured channels:
`X_ACR_Mot.PV`, `X_ACR_Mot.SV`, `X_ACR_Mot.TV`, `X_ACR_Pmp.PV`, `X_ACR_Pmp.SV`,
`X_ACR_Pmp.TV`, `X_Temp.SV`, `X_Pres.SV`, `Barometer`, `Temperature`.
(`X` prefix = pump unit A/B/C.) **Not** the "8 channels" Phase 1 assumed — this is 10 channels +
timestamp. Re-verify exact header strings byte-level on load.

### Clean/corrupt status — CORRECTED from the original "datasets 1–9 / #9 corrupt" note
- **8 valid operational files** form the clean analysis pool: Pump_A ×3, Pump_B ×3, Pump_C ×2.
- **Pump_C_Day3 is NOT a valid operational day.** Two independent reasons converge:
  1. **Source says so.** The official CIRA description states the dataset is *eight* CSV files, and
     that **pump C data is unavailable for one day because the pump was turned off.** Pump_C_Day3 is
     that off day — it should not contain valid operational data.
  2. **Encoding corruption.** Its values are mangled by a decimal/thousands-separator fault, e.g.
     `X_Temp.SV = 19.194.183.349.609.300` (five decimal points — not a number). The other 8 files
     show clean values (`X_Temp.SV ≈ 23.95`). This is a *systematic* separator-locale mangling of this
     file specifically, not random NaN.
- **Disposition (Section 2.6 — examine, don't silently discard):** Pump_C_Day3 is **excluded from the
  clean pool but RETAINED for Thread-A forensic note.** Its existence answers an E.4.2 forensic
  question directly ("why was the pump off?" → it was deliberately off that day). Whether the encoding
  is byte-level recoverable is a Thread-A question; we do not guess decimal positions to "repair" it.
- Net effect: the local 9-file set, minus Pump_C_Day3, **matches the official 8-file dataset.**

### OPEN AUTHENTICATION ITEM — UNITS DO NOT MATCH PHYSICS (Section 2.3) — BLOCKING for Edge-2
The source unit table appears inconsistent with the asset nameplate and the observed values. Must be
resolved before any deviation is measured in "real engineering units" (Edge 2 depends on it):
- `X_Pres.SV` is labelled **bar**, but clean values are ≈ **0.78** — physically impossible as the
  running outlet pressure of a 40 bar / 450 m-head pump. Suggests a different unit/scale (or a
  normalised/ratio quantity), not bar.
- `X_ACR_Mot.PV` is labelled vibration velocity **m/s**, value ≈ **0.0011**. If actually **mm/s**
  (ISO 10816 convention), 0.0011 m/s = 1.1 mm/s = a sane "good" vibration level. Strongly suggests the
  unit is mm/s, not m/s.
- `X_ACR_Mot.SV` labelled **m/s²** (peak accel) — to be checked against the same logic.
- **Action:** Thread A must authenticate every channel's true unit against the nameplate physics +
  ISO 10816/13373 vibration conventions before Edge-2 deviation measurement. Stated units are treated
  as *candidate*, not authoritative (Section 2.3).

### Asset note
Source table restates "Power 10 kW" for the pump alongside "110 kW" motor power shaft. **Binding rule
holds: 110 kW is the asset; 10 kW is a sub-duty test point and is NEVER used in any physics equation.**

### Open verification items (script-driven, before any re-segmentation)
- [ ] Byte-level read of all 9 files: exact headers, row counts, encoding.
- [ ] Confirm Pump_C_Day3 is the only file with the multi-dot separator mangling; quantify extent.
- [ ] Establish true sampling cadence per file from the Timestamp column (the minute-level display
      `10:00` may hide sub-minute sampling — measure the real interval).
- [ ] Authenticate units per channel against asset physics (resolve the bar / mm/s discrepancies).

---

## NEXT STEP
Thread A (primary), step 1: a **forensic inspector script** (no `read_csv` defaults that coerce) that
reads raw bytes of all 9 files and reports the verification items above. Only after that — and after
the unit authentication — does re-segmentation (E.4.2) begin.