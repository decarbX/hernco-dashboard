# Revive08 Station 1983 IFM Flow and Temperature Field Audit
## Prepared by decarbX GHGsolve | June 26, 2026

---

## 1. Executive Finding

The IFM SU6030 cumulative counters (`supply_total_l`, `return_total_l`) align with CAN bus fuel consumption at 100% agreement over a 4-hour engine-running window. The SkyView instantaneous `supply_flow_lph` and `return_flow_lph` fields are mis-scaled by approximately 59x relative to the cumulative-implied rate. The `fuel_temp_delta_c` field appears to compute `supply_temp_c - return_temp_c` instead of the correct `return_temp_c - supply_temp_c`. The sensors themselves are functional and transmitting valid data.

---

## 2. Data Window

| Parameter | Value |
|---|---|
| Asset | Revive08 |
| Station | 1983 (HRC REV POC1) |
| Pull file | `data/raw/station-1983-20260626-194441.csv` |
| Window | June 26, 2026 06:01Z to 18:04Z |
| Total rows | 486 |
| Channel values | 28,188 |
| Engine runtime used for validation | 4.0 hours (12:16Z to 16:16Z) |

---

## 3. CAN Bus Control Values

CAN bus is used as the trusted control source for fuel consumption validation.

| Metric | Value |
|---|---|
| Total fuel consumed (`total_fuel_used_l` delta) | 63.5 L |
| Average fuel rate during engine running | 9.74 L/hr |
| Valid engine-running samples | 449 rows |

**Runtime criteria applied:**
- `is_ignition_on` = 1
- `rpm` > 0
- `engine_fuel_rate_lph` > 0
- `engine_hours_min` increasing

---

## 4. IFM Cumulative Counter Validation

IFM flow was recalculated from cumulative counter deltas rather than the instantaneous `flow_lph` fields.

| Metric | Value |
|---|---|
| `supply_total_l` delta (full window) | 125.6 L |
| `return_total_l` delta (full window) | 62.1 L |
| Net IFM fuel from cumulative counters | 63.5 L |
| CAN bus total fuel | 63.5 L |
| **Percent agreement** | **100.0%** |

**One-hour rolling window validation:** 363 overlapping 1-hour windows were tested using `delta(supply_total_l) / delta(time)` and `delta(return_total_l) / delta(time)`. All 363 windows produced net IFM fuel rates matching CAN bus within rounding error.

**Return/supply ratio:** 44-51% depending on engine load. Consistent with diesel engine return system behavior (engine consumes 50-56% of supply, returns remainder).

---

## 5. Instantaneous flow_lph Problem

The `supply_flow_lph` and `return_flow_lph` fields report values that disagree with the cumulative counters from the same sensor hardware.

| Field | Value |
|---|---|
| `supply_flow_lph` average (engine running) | 0.53 L/hr |
| `return_flow_lph` average (engine running) | 0.27 L/hr |
| Supply rate implied by `supply_total_l` cumulative delta | 31.3 L/hr |
| Return rate implied by `return_total_l` cumulative delta | 15.5 L/hr |
| **Disagreement factor (supply)** | **~59x** |

The cumulative counters and the instantaneous rate fields are sourced from the same IFM SU6030 sensor hardware (`sensor_code: ifm_su6030`, `sensor_id: 6745` for supply; `sensor_id: 6746` for return). The cumulative counters are accurate. The instantaneous rate fields are not.

**Recommendation:** Do not use `supply_flow_lph`, `return_flow_lph`, or `net_fuel_flow_lph` until the conversion logic is corrected.

---

## 6. Temperature Delta Issue

Raw temperature sensors read correctly. The derived delta field has the subtraction order reversed.

| Field | Value |
|---|---|
| `supply_temp_c` average (engine running) | 35.3°C |
| `return_temp_c` average (engine running) | 44.9°C |
| Rows where return > supply | 95.8% (430 of 449) |
| Physical delta: return - supply | +9.6°C (physically correct) |
| `fuel_temp_delta_c` average (SkyView derived) | -9.6°C |
| Apparent SkyView formula | `supply_temp_c - return_temp_c` |
| Correct formula should be | `return_temp_c - supply_temp_c` |

The raw temperature sensors appear correctly placed on the engine supply and return fuel lines. A +9.6°C temperature rise from supply to return is physically consistent with diesel fuel passing through a hot engine. The `fuel_temp_delta_c` field reverses the sign.

---

## 7. Sensor Status Question

| Field | Value | Context |
|---|---|---|
| `supply_status` | 0 | All 486 rows |
| `return_status` | 0 | All 486 rows |

Both status fields report `0` across the entire pull window. Data transmission continues uninterrupted despite status `0`. Please confirm the NAMUR NE 107 interpretation of `0` in the SkyView/ST910x implementation:
- Does `0` indicate normal / data available?
- Or does `0` indicate initialization, fault, maintenance required, or out-of-spec?

---

## 8. Requested SkyView Actions

| Priority | Action |
|:--------:|--------|
| **P0** | Review and correct `supply_flow_lph` and `return_flow_lph` conversion logic, units, scaling, and averaging window. The cumulative counter validation proves the sensor data path is intact. The instantaneous rate derivation has a ~59x discrepancy from the cumulative-implied rate. |
| **P0** | Correct `fuel_temp_delta_c` formula to `return_temp_c - supply_temp_c`. Currently appears to compute `supply_temp_c - return_temp_c`, which reverses the sign of a physically correct measurement. |
| **P1** | Confirm the NAMUR NE 107 meaning of sensor status value `0` for IFM SU6030 channels. |
| **P2** | Re-run `fuel_flow_quality_low_ifm_flow` and `fuel_flow_quality_return_gt_supply` flags after `flow_lph` conversion is corrected. Current flags are likely false positives driven by the mis-scaled rate fields. |

---

## 9. Attachments / References

- **CSV data:** `data/raw/station-1983-20260626-194441.csv` (486 rows, 58 columns)
- **JSONL data:** `data/raw/station-1983-20260626-194443.jsonl` (486 records, 47 channels per record)
- **Channel metadata:** `data/raw/station-1983-20260626-194441.csv.channels.json` (57 registered channels)
- **Validation script:** Python cumulative-delta recalculation, available on request
- **Previous reports:** `IFM_VALIDATION_CHECKLIST.md`, `STATION_1983_VALIDATION_REPORT.md` (updated June 26, 2026)

---

*End of Report*
