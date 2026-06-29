# Pumponova — Dataset & Asset Reference

> Permanent reference for the CIRA SACIP centrifugal-pump dataset used by Pumponova.
> Source: CIRA (Italian Aerospace Research Centre), Zenodo DOI 10.5281/zenodo.15301820.
> **Status of units:** the per-channel units below are taken from the source description but are
> **PROVISIONAL** — several disagree with the asset physics (see §4). They will be authenticated and
> corrected under Thread A, and this file updated to mark each unit VERIFIED. Until then, treat units
> as candidate, not authoritative (rigor discipline §2.3).

---

## 1. What the dataset is

Operational data from centrifugal pumps that supply demineralised water to the boilers of a heating
plant providing steam to CIRA's research test facilities. Each CSV is one pump's data for one
operational day. Filenames identify the pump unit (A, B, C) and the day.

---

## 2. Files

The official dataset is **8 CSV files** (3 operational days across pumps A, B, C). Locally there are
**9 files** — the extra one, `Pump_C_Day3`, is the day pump C was **turned off** and is additionally
**encoding-corrupted** (see §5). It is excluded from the clean analysis pool and retained only for
forensic note.

| Pump | Day1 | Day2 | Day3 |
|------|------|------|------|
| A | valid | valid | valid |
| B | valid | valid | valid |
| C | valid | valid | excluded (pump off + corrupted) |

**Clean analysis pool: 8 files** (A×3, B×3, C×2).

---

## 3. Column schema (11 columns)

Column 1 is the timestamp; the remaining 10 are measured channels. The `X` prefix denotes the pump
unit label (A, B, or C).

| Column | Description | Unit (PROVISIONAL — see §4) |
|--------|-------------|------------------------------|
| `Timestamp` | Time the measurement was taken. Format: `DD-MM-YYYY hh:mm` (as displayed). | — |
| `X_ACR_Mot.PV` | Vibrational velocity measured by the **motor** accelerometer. | m/s *(provisional; likely mm/s)* |
| `X_ACR_Mot.SV` | Peak value measured by the **motor** accelerometer. | m/s² *(provisional)* |
| `X_ACR_Mot.TV` | Contact temperature of the **motor** accelerometer. | °C *(provisional)* |
| `X_ACR_Pmp.PV` | Vibrational velocity measured by the **pump** accelerometer. | m/s *(provisional; likely mm/s)* |
| `X_ACR_Pmp.SV` | Peak value measured by the **pump** accelerometer. | m/s² *(provisional)* |
| `X_ACR_Pmp.TV` | Contact temperature of the **pump** accelerometer. | °C *(provisional)* |
| `X_Temp.SV` | Motor casing temperature. | °C *(provisional)* |
| `X_Pres.SV` | Outlet fluid pressure from the pump. | bar *(provisional; value ≈0.78 inconsistent with 40-bar asset)* |
| `Barometer` | Atmospheric pressure. | mbar *(provisional)* |
| `Temperature` | Ambient temperature. | °C *(provisional)* |

**Observed clean value ranges (from inspection, indicative only — not a measured statistic yet):**
`X_ACR_Mot.PV ≈ 0.0011`, `X_ACR_Mot.SV ≈ 0.461`, `X_ACR_Mot.TV ≈ 24.0`, `X_Pres.SV ≈ 0.78`,
`Barometer ≈ 1012`, `Temperature ≈ 25.4`. (To be replaced with measured per-channel ranges from the
Thread-A forensic inspector.)

---

## 4. UNIT AUTHENTICATION — OPEN (to be resolved in Thread A)

The source units do not all agree with the asset physics. These are open items, **blocking for any
deviation measurement in engineering units (Edge 2):**

1. **`X_Pres.SV` labelled `bar`, value ≈ 0.78.** A pump rated 40 bar / 450 m head cannot have a
   running outlet pressure below 1 bar. The label or scale is suspect — possibly a different unit,
   a gauge/offset, or a normalised quantity. **UNRESOLVED.**
2. **`X_ACR_Mot.PV` / `X_ACR_Pmp.PV` labelled `m/s`, value ≈ 0.0011.** ISO 10816 vibration velocity
   is conventionally **mm/s**; 0.0011 m/s = 1.1 mm/s, a sane "good" level. Unit is likely **mm/s**,
   not m/s. **UNRESOLVED.**
3. **`X_ACR_Mot.SV` / `X_ACR_Pmp.SV` labelled `m/s²`** (peak accel) — check magnitude against the
   same reasoning. **UNRESOLVED.**
4. All other channels (`.TV`, `X_Temp.SV`, `Barometer`, `Temperature`) — confirm against physical
   plausibility. **UNRESOLVED.**

> **When resolved:** update §3 to mark each unit VERIFIED with the authenticated unit and the
> physical/literature basis (nameplate, ISO 10816/13373), and remove the *(provisional)* tags.

---

## 5. The corrupted file (`Pump_C_Day3`)

- The source states pump C data is unavailable for one day because the pump was **turned off**.
  `Pump_C_Day3` is that day — not a valid operational record.
- Its values are additionally mangled by a **decimal/thousands-separator fault**, e.g.
  `X_Temp.SV = 19.194.183.349.609.300` (five decimal points — not parseable as a number). The other
  8 files show clean values (`X_Temp.SV ≈ 23.95`).
- **Disposition:** excluded from the clean pool; retained for Thread-A forensic note. Encoding
  recoverability is a Thread-A question — decimal positions are not guessed to "repair" it.

---

## 6. Asset nameplate (LOCKED — all physics references these)

**Binding rule:** the asset is **110 kW**. The "10 kW" figure that appears in the source pump table
is a sub-duty test point and is **NEVER used in any physics equation.**

### Electric motor
| Power shaft | Speed | Voltage | Poles | Frame size (H) |
|-------------|-------|---------|-------|----------------|
| 110 kW | 2980 rpm | 400 V | 2 | 315 mm |

### Mechanical multistage pump
| Power | Flow rate | Pump head | Max pressure | No. of impellers |
|-------|-----------|-----------|--------------|------------------|
| 10 kW *(sub-duty test point — do NOT use in physics)* | 45 m³/h | 450 m | 40 bar | 7 |

Derived: nameplate hydraulic power P = ρgQH/η ≈ 55 kW.

---

## 7. Change log
- 2026-06-29 — Created. Schema + descriptions from source; units PROVISIONAL pending Thread-A
  authentication; `Pump_C_Day3` documented as off-day + encoding-corrupt.
- *(pending)* — Units authenticated in Thread A; §3 and §4 to be updated VERIFIED.
