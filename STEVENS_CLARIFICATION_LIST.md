# ORBCOMM / Stevens — Station 1983 Channel Clarification List
## decarbX GHGsolve | Prepared: June 25, 2026

---

## Context

Station 1983 (HRC REV POC1, Hernco Revive08) has 57 channels registered. Latest pull: 332 readings, 151 engine-running. This list identifies channels that are missing, dead, or reporting invalid data — all of which block customer-facing dashboard analytics.

---

## Part 1: Missing J1939 PGN / Channel Mappings (9 mapping groups / 13 missing fields)

The following standard CAN bus parameters are not mapped in the Station 1983 channel catalog. Most ECMs broadcast the first 9 — they may need ST910x PGN configuration to enable. The final 4 require additional sensors beyond CAN bus mapping.

| # | Target Field | Typical J1939 PGN | Priority | Use Case |
|---|---|---|---|---|
| 1 | Ambient Air Temperature | 65269 (Ambient Conditions) | P0 | If ECM broadcasts IAT or ambient temp, map it. Required for temperature-adjusted fuel analytics. |
| 2 | Engine Load (percent) | 61443 (Electronic Engine Controller 2) | P1 | Standard ECM parameter. Core operational context. |
| 3 | Actual Engine Torque (percent) | 61444 (EEC 3) | P1 | Standard ECM parameter. Driver/load analysis. |
| 4 | Driver Demand Torque (percent) | 61444 (EEC 3) | P2 | Operator behavior analysis. |
| 5 | Accelerator Pedal Position (percent) | 61443 (EEC 2) | P1 | Standard ECM parameter. Operator input vs engine response. |
| 6 | Fuel Rail Pressure | 65263 (Fuel Consumption) | P2 | High-pressure common rail diagnostic. |
| 7 | Fuel Delivery Pressure | 65263 | P2 | Low-pressure supply diagnostic. |
| 8 | Fuel Level 1 | 65276 (Dash Display) | P2 | Tank level gauge. |
| 9 | Fuel Level 2 | 65276 | P2 | Secondary tank level if dual-tank. |
| 10 | FuelLevel_FLS | N/A — requires FLS sensor | P3 | Fuel tank level with integrated temperature sensor. |
| 11 | FuelTemperature_FLS | N/A — requires FLS sensor | P3 | Fuel tank temperature from FLS sender. |
| 12 | TirePressure | N/A — requires TPMS sensor | P3 | Tire pressure monitoring. |
| 13 | TireTemperature | N/A — requires TPMS sensor | P3 | Tire temperature from TPMS. |

**Question for Stevens:** For items 1–9, are these PGNs broadcast by the Revive08 ECM? If yes, can they be added to the Station 1983 channel list? Items 10–13 require additional sensor hardware — included for completeness but not expected from J1939 mapping alone.

---

## Part 2: Dead Derived Fields

These channels exist in the catalog but produce no output — or negligible output.

| Field | Channel ID | Issue | Evidence |
|---|---|---|---|
| `fuel_saved_per_km_l` | 18385 | **Completely empty** | 0 of 332 rows populated |
| `fuel_saved_total_l` | 18386 | **Completely empty** | 0 of 332 rows populated |
| `carbon_saved_per_km_t` | 18382 | **Nearly empty** | 4 of 332 rows (1.2%), static values |
| `carbon_saved_total_metric_t` | 18383 | **Nearly empty** | 4 of 332 rows (1.2%) |

**Question for Stevens:** These are the primary customer-facing value metrics. What inputs do the fuel-saved and carbon-saved calculations require? Is there a pipeline activation step needed, or are they dependent on IFM flow data that's currently invalid?

---

## Part 3: IFM Flow Under-Reporting

The IFM SU6030 sensors are transmitting data but the flow values are objectively wrong.

| Observation | Value | Expected |
|---|---|---|
| Supply flow rate (engine running) | Mean 0.94 L/hr | Should approximate engine fuel rate (~16 L/hr) |
| Return flow rate (engine running) | Mean 0.88 L/hr | Should be lower than supply (diesel return is ~50-70% of supply) |
| Net IFM flow vs Engine CAN fuel rate | 98.9% MAPE | Should be ≤ 5% |
| `low_ifm_flow` quality flag | Active on 100% of engine-running rows | Should be 0% |
| Supply sensor status | '0' (not OK) | Should report OK |
| Return sensor status | '0' (not OK) | Should report OK |

**Finding:** The sensors have power and communication — they transmit data. The problem is flow measurement accuracy. The flow rates are ~17x below the engine CAN bus fuel rate, and return flow is approximately equal to supply flow (physically impossible for a diesel return system).

**Question for Stevens:** 
1. What does status '0' mean for the IFM SU6030? (OK / Maintenance Required / Out of Spec / Functional Check / Failure)
2. When were the IFM sensors last calibrated?
3. What fuel type and viscosity is configured in the IFM sensor settings?
4. Is there a known issue with air in the fuel lines near the sensor mounting points?

---

## Summary

| Category | Items | Impact |
|---|---|---|
| Missing J1939 mappings | 9 mapping groups (13 fields total) | Blocks ambient/torque/load/accelerator/fuel-level/tire analytics |
| Dead derived fields | 4 channels | Blocks customer-facing fuel-saved and carbon-saved reporting |
| IFM under-reporting | 2 sensors | Blocks validated fuel flow measurement; all fuel metrics are CAN-only estimates |

---

## Contact

Please route to the appropriate ORBCOMM / Stevens engineering contact. Station 1983 channel list and raw CSV data available on request.

decarbX GHGsolve | 007 AI Command Center
