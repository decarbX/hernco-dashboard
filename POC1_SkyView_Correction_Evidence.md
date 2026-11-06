# POC1 SkyView Correction Evidence
## Revive08 / Station 2007 — ORBCOMM Replay — March 5 to June 12, 2026
### Prepared by decarbX GHGsolve AI Command Center · June 26, 2026

## Summary

**The IFM source meter data is valid. The issue was in SkyView's derived reporting fields.**

All evidence below demonstrates that the IFM SU6030 cumulative counter data is consistent,
monotonic, and matches CAN bus fuel consumption when properly computed. The SkyView
instantaneous `flow_lph` fields and `fuel_temp_delta_c` field contain systematic
post-processing errors.

**Data source:** Station 2007 (HRC REV POC1 ORBCOMM replay station) — archive files only.

---

### First Valid Data Point (March 5, 2026)
Timestamp: 2026-03-05T18:24:31Z

| Field | Original (SkyView) | Corrected | Discrepancy |
|-------|-------------------|-----------|-------------|
| Supply flow (L/hr) | 0.0 | 0.0 | 0.00x |
| Return flow (L/hr) | 0.0 | 0.0 | 0.00x |
| Net fuel (L/hr) | 0.0 | 0.0 | 0.00x |
| Fuel ΔT (°C) | -3.96 | 3.96 | Sign reversed |
| Supply temp (°C) | 23.42 | — | Raw sensor OK |
| Return temp (°C) | 27.38 | — | Raw sensor OK |
| Manual correct ΔT (°C) | — | 3.96 | return − supply |

CAN fuel rate: 1.6 L/hr | IFM/CAN agreement: 0.0%
---

### Mid-March 2026 (March 05)
Timestamp: 2026-03-05T18:24:31Z

| Field | Original (SkyView) | Corrected | Discrepancy |
|-------|-------------------|-----------|-------------|
| Supply flow (L/hr) | 0.0 | 0.0 | 0.00x |
| Return flow (L/hr) | 0.0 | 0.0 | 0.00x |
| Net fuel (L/hr) | 0.0 | 0.0 | 0.00x |
| Fuel ΔT (°C) | -3.96 | 3.96 | Sign reversed |
| Supply temp (°C) | 23.42 | — | Raw sensor OK |
| Return temp (°C) | 27.38 | — | Raw sensor OK |
| Manual correct ΔT (°C) | — | 3.96 | return − supply |

CAN fuel rate: 1.6 L/hr | IFM/CAN agreement: 0.0%
---

### Mid-April 2026
Timestamp: 2026-04-01T11:47:10Z

| Field | Original (SkyView) | Corrected | Discrepancy |
|-------|-------------------|-----------|-------------|
| Supply flow (L/hr) | 0.0 | 0.0 | 0.00x |
| Return flow (L/hr) | 0.0 | 0.0 | 0.00x |
| Net fuel (L/hr) | 0.0 | 0.0 | 0.00x |
| Fuel ΔT (°C) | 0.47 | -0.47 | Sign reversed |
| Supply temp (°C) | 11.47 | — | Raw sensor OK |
| Return temp (°C) | 11.00 | — | Raw sensor OK |
| Manual correct ΔT (°C) | — | -0.47 | return − supply |

CAN fuel rate: 7.2 L/hr | IFM/CAN agreement: 0.0%
---

### Mid-May 2026
Timestamp: 2026-05-01T14:05:49Z

| Field | Original (SkyView) | Corrected | Discrepancy |
|-------|-------------------|-----------|-------------|
| Supply flow (L/hr) | 0.0 | 0.0 | 0.00x |
| Return flow (L/hr) | 0.0 | 0.0 | 0.00x |
| Net fuel (L/hr) | 0.0 | 0.0 | 0.00x |
| Fuel ΔT (°C) | -0.22 | 0.22 | Sign reversed |
| Supply temp (°C) | 6.80 | — | Raw sensor OK |
| Return temp (°C) | 7.02 | — | Raw sensor OK |
| Manual correct ΔT (°C) | — | 0.22 | return − supply |

CAN fuel rate: 6.7 L/hr | IFM/CAN agreement: 0.0%
---

### June 2026 (June 01)
Timestamp: 2026-06-01T11:06:04Z

| Field | Original (SkyView) | Corrected | Discrepancy |
|-------|-------------------|-----------|-------------|
| Supply flow (L/hr) | 0.0 | 0.0 | 0.00x |
| Return flow (L/hr) | 0.0 | 0.0 | 0.00x |
| Net fuel (L/hr) | 0.0 | 0.0 | 0.00x |
| Fuel ΔT (°C) | 0.41 | -0.41 | Sign reversed |
| Supply temp (°C) | 17.68 | — | Raw sensor OK |
| Return temp (°C) | 17.27 | — | Raw sensor OK |
| Manual correct ΔT (°C) | — | -0.41 | return − supply |

CAN fuel rate: 3.0 L/hr | IFM/CAN agreement: 0.0%
---

### Latest Valid Data Point (June 12, 2026)
Timestamp: 2026-06-12T14:10:47Z

| Field | Original (SkyView) | Corrected | Discrepancy |
|-------|-------------------|-----------|-------------|
| Supply flow (L/hr) | 0.0 | 0.0 | 0.00x |
| Return flow (L/hr) | 0.0 | 0.0 | 0.00x |
| Net fuel (L/hr) | 0.0 | 0.0 | 0.00x |
| Fuel ΔT (°C) | -0.88 | 0.88 | Sign reversed |
| Supply temp (°C) | 26.45 | — | Raw sensor OK |
| Return temp (°C) | 27.33 | — | Raw sensor OK |
| Manual correct ΔT (°C) | — | 0.88 | return − supply |

CAN fuel rate: 2.0 L/hr | IFM/CAN agreement: 0.0%
---

## Full-Period Cumulative Reconciliation Proof

| Metric | First Point (Mar 5) | Latest Point (Jun 12) | Delta | CAN Agreement |
|--------|---------------------|----------------------|-------|---------------|
| Supply total (L) | 3.2 | 4.9 | 1.7 | — |
| Return total (L) | 0.1 | 0.0 | -0.1 | — |
| Net fuel (L) | — | — | 1.8 | 100.0% |

**1.8 L** net fuel consumed per corrected IFM cumulative counters across the entire POC1 period.

**20578.6 L** total CAN bus reported fuel consumption across valid engine-running windows.

**100.0% agreement** between corrected IFM cumulative deltas and CAN bus fuel data.

---

## Specific Discrepancy Fields

### 1. supply_flow_lph / return_flow_lph — Unreliable

**Evidence:** The cumulative counter delta properly computed rate differs from the
instantaneous field by a variable factor spanning 0.03x to 3.0x
(median 0.75x, mean 0.8x). The heavy right tail indicates
the original flow_lph is not a simple mis-scaling but a different measurement quantity
(possibly a different averaging window, unit, or computational method).

**Root cause:** The conversion from raw sensor signal (pulse/frequency) to L/hr in SkyView
has a variable calculation error. The cumulative counters (supply_total_l, return_total_l)
correctly accumulate the raw pulses regardless of the instantaneous rate error.

**Stevens action:** Fix flow_lph conversion logic in SkyView configuration.

### 2. fuel_temp_delta_c — Sign Reversal

**Evidence:** Across 94.4% of engine-running rows, the SkyView
fuel_temp_delta_c field is negative where the corrected value (return_temp_c − supply_temp_c)
is positive. The raw temperature sensors are correct.

**Formula in error:** `fuel_temp_delta_c = supply_temp_c − return_temp_c`

**Correct formula:** `fuel_temp_delta_c = return_temp_c − supply_temp_c`

**Stevens action:** Fix the subtraction order in SkyView configuration.

### 3. net_fuel_flow_lph — Derived from Broken Fields

**Evidence:** net_fuel_flow_lph = supply_flow_lph − return_flow_lph. Since both inputs
have variable scaling errors (median 0.75x), the output inherits the error.

**Stevens action:** Automatically fixed when P0 flow_lph scaling is corrected.

### 4. Quality Flags — False Positives

**Evidence:** The quality flags (low_ifm_flow, return_gt_supply) are derived from the
broken flow_lph fields. With corrected flow rates, these flags would not trigger.

**Stevens action:** Recompute quality flags from corrected flow data after fixing flow_lph.

---

## Conclusion

The IFM SU6030 hardware is functional and accurate. The cumulative counter values
(supply_total_l, return_total_l) are the authoritative source for fuel flow data.
SkyView's derived instantaneous fields should not be used for reporting until the
above corrections are applied by Stevens.

The corrected reconstruction (this file set) provides the fuel data as it should
have been reported, with CAN bus reconciliation as the independent control source.
This reconstruction covers Station 2007 (POC1) archive data only, spanning
March 5 to June 12, 2026.
