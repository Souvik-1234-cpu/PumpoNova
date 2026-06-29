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

## RAW DATASET INVENTORY (Section 2.1 — stated explicitly; Section 2.5 — verify on load)

> The raw datasets are NOT YET on disk in `data/raw/`. To be added by Souvik. Recorded here as the
> stated state of the data before it is loaded; **every property below is re-verified against the
> actual files when they land** (shape, columns, units, cadence, NaN extent) before any analysis keys
> logic to them.

- **Total raw datasets: 9** (referenced as datasets 1–9).
- **Usable training/analysis pool: datasets 1–8.**
- **Dataset 9: corrupted** — contains NaN / NS values; **excluded from the clean usable pool.**

**Important (Section 2.6 — root-cause, not discard):** Dataset 9 is **excluded from training/clean
analysis but RETAINED for forensic examination under Thread A.** Part E / E.4.2 treats discarded
fragments as forensic events to explain, not delete. A NaN-laden dataset is exactly the kind of artifact
Phase 1's usability filter would have thrown away unexamined — the NaN pattern itself may carry a
signature (sensor dropout, logging failure, a trip that corrupted the write) worth understanding. It is
therefore **not deleted**; it is flagged and parked for Thread A forensic review, separate from the
clean pool used for any analysis.

**Open verification items when data lands (do before any analysis):**
- [ ] Confirm 9 files present in `data/raw/`; confirm which is dataset 9.
- [ ] Verify column schema, units, and sampling cadence of datasets 1–8 against the asset's expected
      8-channel-at-1s assumption (do not assume — measure).
- [ ] Characterise the dataset-9 corruption: which channels, what fraction NaN, contiguous vs
      scattered, and whether a physical break-cause is implied.

---

## NEXT STEP
Thread A (primary) begins once the raw datasets are in `data/raw/` and the verification items above
are cleared. First Thread-A action: re-segment raw data without discarding short/broken segments,
characterising each break in the Edge-2 vocabulary (E.4.2).
