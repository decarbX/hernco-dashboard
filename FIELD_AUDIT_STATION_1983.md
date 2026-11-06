# Station 1983 Field-Completeness Audit
## SkyView360 GHGsolve — Latest Pull: June 25, 2026 12:16 PM EDT
### Prepared: June 25, 2026 | decarbX GHGsolve

**Data window:** 332 readings, 322 ignition-ON, 151 engine-running (IG-ON + RPM > 0)
**Channel catalog:** 57 registered channels

---

## Audit Legend

| Status | Meaning |
|---|---|
| **Present** | Field exists in SkyView360 channel catalog |
| **Parsed** | Field appears as a column in raw CSV/JSONL output |
| **Populated** | Field has non-empty values |
| **Validated** | Field has non-zero, meaningful values during engine-running periods |
| **Blocked** | Field is absent from catalog, empty in CSV, or all-zero |

---

## Fernando Target Fields Audit

| Field | Present | Parsed | Populated | Validated | Blocked | Detail |
|---|---|---|---|---|---|---|
| **AmbientAirTemperature** | ❌ | ❌ | ❌ | ❌ | ✅ | Not in channel catalog. No temp, pressure, or weather data broadcast on this station. |
| **TirePressure** | ❌ | ❌ | ❌ | ❌ | ✅ | Not in catalog. No tire-related channels exist. |
| **TireTemperature** | ❌ | ❌ | ❌ | ❌ | ✅ | Not in catalog. No tire-related channels exist. |
| **ActualEngineTorque** | ❌ | ❌ | ❌ | ❌ | ✅ | Not in catalog. No J1939 torque PGN mapped. |
| **DriverDemandTorque** | ❌ | ❌ | ❌ | ❌ | ✅ | Not in catalog. No torque channels of any kind. |
| **EngineLoad** | ❌ | ❌ | ❌ | ❌ | ✅ | Not in catalog. No load channels. |
| **AcceleratorPosition** | ❌ | ❌ | ❌ | ❌ | ✅ | Not in catalog. No accelerator/pedal channels. |
| **FuelRailPressure** | ❌ | ❌ | ❌ | ❌ | ✅ | Not in catalog. No pressure channels of any kind. |
| **FuelDeliveryPressure** | ❌ | ❌ | ❌ | ❌ | ✅ | Not in catalog. |
| **FuelLevel_FLS** | ❌ | ❌ | ❌ | ❌ | ✅ | Not in catalog. No fuel level channels of any kind. |
| **FuelTemperature_FLS** | ❌ | ❌ | ❌ | ❌ | ✅ | Not in catalog. No fuel tank temp channels. |
| **FuelLevel1** | ❌ | ❌ | ❌ | ❌ | ✅ | Not in catalog. |
| **FuelLevel2** | ❌ | ❌ | ❌ | ❌ | ✅ | Not in catalog. |
| **fuel-saved (per_km + total)** | ✅ | ✅ | ❌ | ❌ | ✅ | Channels exist: `fuel_saved_per_km_l` (18385), `fuel_saved_total_l` (18386). BOTH FIELDS COMPLETELY EMPTY in CSV — 0 of 332 rows have values. |
| **carbon-saved (per_km + total)** | ✅ | ✅ | ⚠️ | ⚠️ | ⚠️ | Channels exist: `carbon_saved_per_km_t` (18382), `carbon_saved_total_metric_t` (18383). Only 4 of 332 rows populated (1.2%). Values appear static: 0.0009–0.00198 range. |

---

## IFM Flow Sensor Deep Dive

| Field | Present | Parsed | Populated | Validated | Detail |
|---|---|---|---|---|---|
| `supply_flow_lph` | ✅ | ✅ | ✅ | ❌ | **Reports data but invalid.** Mean 0.94 L/hr during engine running. Engine CAN bus fuel rate = 16.37 L/hr. Factor ~17x too low. |
| `return_flow_lph` | ✅ | ✅ | ✅ | ❌ | **Reports data but invalid.** Mean 0.88 L/hr. Return ≈ Supply (impossible for diesel). |
| `supply_status` | ✅ | ✅ | ✅ | ❌ | Value = '0' on all rows. NOT reporting 'OK'. Likely 'Failure' or 'Out of Spec' per NAMUR NE 107. |
| `return_status` | ✅ | ✅ | ✅ | ❌ | Value = '0' on all rows. Same failure state. |
| `net_fuel_flow_lph` | ✅ | ✅ | ✅ | ❌ | Mean 0.19 L/hr. 98.9% MAPE vs engine CAN bus fuel rate (16.37). |
| `fuel_flow_quality_low_ifm_flow` | ✅ | ✅ | ✅ | ❌ | **Flagged '1' (True) on 100% of engine-running rows.** Confirms IFM flow is below valid threshold. |
| `fuel_flow_quality_return_gt_supply` | ✅ | ✅ | ✅ | ❌ | Flagged on 60 of 151 engine-running rows. |

**IFM Conclusion:** The IFM SU6030 sensors have power and communication (they transmit data) but the flow measurements are invalid — near-zero flow rates, return ≈ supply (physically impossible for a diesel return system), and all quality flags confirm low/unreliable flow. The "NON-FUNCTIONAL" label is correct: the sensors need physical recalibration or replacement.

---

## Existing Channel Population Summary

### Fully Validated (Populated + Non-Zero During Engine Running)

| Channel | Mean Value (Running) | Notes |
|---|---|---|
| `rpm` | Varies (0–1,666) | Healthy |
| `speed_kph` | Varies (0–105) | 89 of 151 non-zero — derate event reduced speed |
| `engine_fuel_rate_lph` | 16.37 | Primary fuel reference |
| `total_fuel_used_l` | Cumulative | Climbing as expected |
| `engine_hours_min` | Cumulative | Climbing as expected |
| `pto_hours_min` | Cumulative | Climbing as expected |
| `trip_fuel_used_l` | Cumulative | Working |
| `trip_distance_km` | Cumulative | Working |
| `odometer_km` | 57,008 | Lifetime |
| `latitude_deg` / `longitude_deg` | Varies | GPS working |
| `is_cellular` / `cell_signal_level` | Working | Strong signal |
| `transport_mode` | Working | Cell/Sat |
| `reason_code` | Varies | Event context |
| `running_idle` | 62 of 151 True | Idle detection working |
| `vehicle_distance_km` | 136 of 151 non-zero | Working |
| `supply_used_interval_l` / `return_used_interval_l` | Cumulative | IFM-derived but unreliable |
| `fuel_temp_delta_c` / `fuel_temp_delta_c_calc` | Varies | IFM-derived but unreliable |

### All-Zero During Engine Running

| Channel | Detail |
|---|---|
| `is_pto_on` | 0 on all rows — no PTO activity today (derate event) |
| `engine_fuel_economy_km_per_l` | 0 — requires movement + fuel |
| `oil_condition_tdn` | 0 — sensor NOT INSTALLED |
| `oil_temp_c` | 0 — sensor NOT INSTALLED |
| `oil_life_pct` | EMPTY entirely |
| `pto_hours_estimated_min` | EMPTY entirely |
| `fuel_saved_per_km_l` | EMPTY entirely |
| `fuel_saved_total_l` | EMPTY entirely |

### Quality Flags Active

| Flag | Running Rows Flagged | Meaning |
|---|---|---|
| `fuel_flow_quality_low_ifm_flow` | 151 of 151 (100%) | IFM flow below minimum valid threshold |
| `fuel_flow_quality_return_gt_supply` | 60 of 151 (40%) | Return > supply — sensor inversion or plumbing issue |
| `fuel_flow_quality_telemetry_gap` | 0 | No telemetry gaps — good |
| `fuel_flow_quality_zero_running` | 0 | No zero-while-running events — good |
| `fuel_flow_quality_net_mismatch` | Empty (no data) or 0 | Net mismatch check not running |

---

## Key Findings

1. **12 of 14 Fernando target fields are NOT in the channel catalog.** AmbientAirTemperature, tire fields, torque fields, load, accelerator position, fuel pressure, fuel level — none exist for Station 1983. These would require ST910x configuration changes to map additional J1939 PGNs or add new sensor types.

2. **fuel-saved fields are completely empty.** `fuel_saved_per_km_l` and `fuel_saved_total_l` channels exist but have zero populated rows. The derived calculation pipeline is not producing output. This requires investigation — the channels are registered but not being computed.

3. **carbon-saved fields are nearly empty.** Only 4 of 332 rows (1.2%) have values, and they appear static/dormant.

4. **IFM paradox resolved.** The IFM sensors transmit data (power + comms work) but flow measurements are objectively wrong (~0.94 L/hr vs 16.37 L/hr engine CAN rate). The `low_ifm_flow` quality flag is active on 100% of readings. Physical sensor inspection is needed — not a wiring or telemetry configuration issue.

5. **PTO state is zero all day.** The derate event on June 24 shut down all PTO activity. `is_pto_on = 0` on every reading. This means v1.3 PTO Fuel Driver cards would show zero PTO activity for today — the "no PTO activity" conditional would hide the section entirely.

---

## Recommendations

| Priority | Action |
|---|---|
| **P0** | Investigate why `fuel_saved` channels produce zero output — they're the primary customer-facing value proposition |
| **P0** | Resolve IFM sensor flow accuracy — physical inspection per IFM_VALIDATION_CHECKLIST.md |
| **P1** | Map J1939 PGNs for at minimum: AmbientAirTemperature (if ECM broadcasts IAT), EngineLoad, AcceleratorPosition. These are standard CAN bus parameters that most ECMs broadcast. |
| **P2** | Investigate carbon-saved calculation — confirm formula and pipeline activation |
| **P3** | Fuel level and tire channels require additional sensors — do not pursue until a clear customer value case exists |
