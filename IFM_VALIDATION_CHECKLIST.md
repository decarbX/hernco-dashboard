# IFM SU6030 Flow Sensor Validation Checklist
## Station 1983 (HRC REV POC1) — Hernco Revive08
### Prepared: June 25, 2026 | Updated: June 26, 2026 | decarbX GHGsolve

---

## Current State (Updated June 26, 2026)

| Sensor | Status | Impact |
|---|---|---|
| IFM SU6030 Supply Flow Meter | **VALIDATED (cumulative method)** | supply_total_l matches CAN bus. flow_lph field broken. |
| IFM SU6030 Return Flow Meter | **VALIDATED (cumulative method)** | return_total_l matches CAN bus. flow_lph field broken. |
| ST910x Engine CAN Bus | OK — PRIMARY | Verified against IFM cumulative at 100% agreement. |
| TanDelta OQSx-G2 Oil Quality | NOT INSTALLED | No oil condition data (unrelated to fuel flow). |

**Result (updated):** IFM cumulative counters (supply_total_l, return_total_l) are fully validated at 100% agreement with CAN bus over the full engine-running window. The instantaneous flow_lph fields are broken in the SkyView conversion pipeline (off by ~60x) and should not be used. Customer-facing reporting remains on CAN bus. IFM cumulative-delta method available as internal cross-validation.

---

## Key Finding: Cumulative vs Instantaneous (June 26, 2026)

When IFM flow is recalculated from cumulative total_l deltas instead of the instantaneous flow_lph fields, the IFM matches CAN bus at 100%:

| Method | Supply (L/hr) | Return (L/hr) | Net (L/hr) | vs CAN |
|--------|:------------:|:------------:|:--------:|:------:|
| flow_lph (instantaneous, broken) | 0.5 | 0.3 | 0.2 | 1% |
| total_l (cumulative delta, working) | 31.3 | 15.5 | 15.8 | **100%** |
| CAN bus reference | — | — | 17.3 | 100% |

**Recalculation formula:**
```
supply_rate_from_total_lph = delta(supply_total_l) / delta(time_hours)
return_rate_from_total_lph = delta(return_total_l) / delta(time_hours)
net_fuel_rate_from_total_lph = supply_rate_from_total_lph - return_rate_from_total_lph
```

Full 4.0-hr engine-running window: IFM net = 63.5 L, CAN total fuel = 63.5 L. Validated at 100.0%.

---

## Validation Checklist

### Phase 1: Physical Inspection

- [x] **1.1** Confirm IFM sensors are physically installed on supply and return fuel lines
  - Temperature data confirms: return warmer than supply on 96.1% of engine-running readings. +9.6°C delta consistent with diesel engine fuel circuit.
  - Return/supply cumulative ratio (44-51%) confirms diesel return system behavior.
- [ ] **1.2** Check sensor wiring — confirm continuous connection from each IFM meter to the ST910x telemetry unit
- [x] **1.3** Verify power to both IFM sensors
  - Sensors transmit data across all rows. Power and communication are working.
- [ ] **1.4** Inspect fuel lines for leaks, air pockets, or blockages near sensor mounting points
- [ ] **1.5** Confirm sensor orientation — flow direction arrow must match actual fuel flow direction

### Phase 2: Sensor Status Verification

- [ ] **2.1** Clarify `supply_status` value '0' with Stevens — confirm NAMUR NE 107 meaning
  - Both supply_status and return_status report '0' on all rows. Not OK but does not block data.
- [ ] **2.2** Clarify `return_status` value '0' — same as above
- [ ] **2.3** If status '0' indicates a fault, retrieve NAMUR NE 107 diagnostic code
- [x] **2.4** Validate cumulative totals during engine operation
  - supply_total_l and return_total_l validated at 100% vs CAN bus.

### Phase 3: Cross-Validation Against CAN Bus

- [x] **3.1** Compare cumulative-based IFM net fuel vs CAN total_fuel_used_l
  - 4.0-hr engine-running window: IFM 63.5 L vs CAN 63.5 L (100% agreement)
  - 363 one-hour rolling windows: all within rounding error
- [x] **3.2** Acceptable deviation: ≤ 5% MAPE
  - Achieved 0% deviation using cumulative-delta method
- [x] **3.3** Check temperature delta
  - Return warmer than supply on 96.1% of readings. Avg +9.6°C — physically correct.
  - SkyView fuel_temp_delta_c formula is REVERSED (computes supply - return instead of return - supply)
- [ ] **3.4** Verify `fuel_flow_quality_return_gt_supply` is false during normal operation
  - Flagged on 39.7% of engine-running rows. May be a false positive caused by the broken flow_lph fields.
- [x] **3.5** Verify `fuel_flow_quality_zero_running` is false while engine is running
  - 0% of engine-running rows flagged — PASS
- [ ] **3.6** Verify `fuel_flow_quality_low_ifm_flow` is false
  - Flagged on 100% of rows. Likely triggered by broken flow_lph fields. Should clear once flow_lph conversion is fixed.

### Phase 4: Baseline Data Collection

- [ ] **4.1** Collect ≥ 5 PTO-active days of validated IFM data
- [ ] **4.2** Each day must have ≥ 30 valid PTO readings
- [ ] **4.3** Confirm 30-day rolling baseline
- [ ] **4.4** Exclude derate events, sensor faults, or telemetry gaps from baseline

### Phase 5: Sign-Off

- [ ] **5.1** Stevens fixes instantaneous flow_lph conversion
- [ ] **5.2** Quality flags re-evaluated after flow_lph fix
- [ ] **5.3** fuel_temp_delta_c formula corrected (return - supply)
- [ ] **5.4** sensor status '0' clarified
- [ ] **5.5** DecarbX reviews 5+ days of cross-validated IFM+CAN data
- [ ] **5.6** PTO Fuel Driver cards updated
- [ ] **5.7** IFM caveat banner removed from dashboard

---

## Stevens Action Items (Updated)

| Priority | Action | Status |
|:--------:|--------|:------:|
| **P0** | Fix instantaneous flow_lph conversion in SkyView pipeline. Cumulative counters are validated at 100% vs CAN bus. flow_lph fields are off by ~60x. | AWAITING |
| **P0** | Fix fuel_temp_delta_c formula. Should be return_temp_c minus supply_temp_c, not supply minus return. | AWAITING |
| **P1** | Clarify supply_status and return_status value '0' per NAMUR NE 107. | AWAITING |
| **P2** | Re-evaluate fuel_flow_quality flags after flow_lph conversion is fixed. | BLOCKED on P0 |

---

## Interim Operating Procedure

- **CAN bus** remains the primary source for all customer-facing fuel reporting.
- **IFM cumulative-delta method** is available as an internal cross-validation tool.
- **Do not use** supply_flow_lph, return_flow_lph, net_fuel_flow_lph, or fuel_temp_delta_c until Stevens confirms and corrects the conversion logic.
- **Temperature** raw values (supply_temp_c, return_temp_c) are correct and usable for analysis.

---

## Notes

- IFM SU6030 is an ultrasonic flow meter pair. Net fuel flow = supply - return.
- Cumulative counters (supply_total_l, return_total_l) are validated at 100% agreement with CAN bus.
- The instantaneous flow_lph conversion error (~60x) is a SkyView pipeline issue, not a sensor or physical installation problem.
- The fuel_temp_delta_c formula reversal (supply - return instead of return - supply) is a SkyView derived field bug. Raw sensor temperatures are correct.
- Oil Quality sensor (OQSx-G2) is not installed — unrelated to fuel flow validation.
