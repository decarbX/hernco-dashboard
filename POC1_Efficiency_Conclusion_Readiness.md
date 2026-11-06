# POC1 Efficiency Conclusion Readiness
## Revive08 / Station 2007 — ORBCOMM Replay — March 5 to June 12, 2026
### Prepared by decarbX GHGsolve AI Command Center · June 26, 2026

## Station Assignments (LOCKED by Sean, June 26, 2026)

| Station | Purpose | Data Used |
|---------|---------|-----------|
| **Station 2007** | HRC REV POC1 ORBCOMM replay | POC1 reconstruction (this report) |
| **Station 1983** | Live feed / POC2 station | POC2 dashboard only (NOT mixed with POC1) |

## Data Window Classification (LOCKED)

| Period | Dates | Classification | Rows | Runtime Hrs | Notes |
|--------|-------|---------------|------|-------------|-------|
| Baseline | Mar 5 – Apr 12, 2026 | Pre-install baseline | 13,867 | 81.4 | Clean pre-install data |
| Stabilization | Apr 13–20, 2026 | EXCLUDED | 0 | 0.0 | Optimizer installed Apr 13; data NOT YET pulled from ORBCOMM |
| Optimization | Apr 21 – Jun 12, 2026 | Post-install validation | 20,696 | To compute | decarbX Fuel Optimizer installed |

**Classification confirmation:**

- ✅ The "optimization" period IS post-install. Optimizer installed April 13, 2026.
- ✅ Carbon saved fields have non-zero data: carbon_saved_per_km_t (422 rows), carbon_saved_total_metric_t (711 rows)
- ✅ IFM supply/return ratio shifted: 59.9% → 69.7% — consistent with changed fuel system behavior post-Optimizer
- ⚠️ Stabilization window (Apr 13–20) not yet pulled from ORBCOMM — 8-day gap in continuous data

**Verdict: BEFORE/AFTER EFFICIENCY COMPARISON IS POSSIBLE. The decarbX Fuel Optimizer was installed on April 13, 2026. Baseline (Mar 5–Apr 12) vs Optimization (Apr 21–Jun 12) represents pre-install vs post-install behavior.**

## Baseline Characterization

| Metric | Value |
|--------|-------|
| Period | Mar 5 – Apr 12, 2026 |
| Total rows | 13,867 |
| Engine-running rows | 9,769 |
| Valid engine runtime hours | 81.4 |
| CAN total fuel consumed | 5530.70 L |
| IFM/CAN agreement | 100.0% |
| IFM return/supply ratio | 59.9% |

## Final Conclusion

**The decarbX Fuel Optimizer IS installed** (April 13, 2026). Post-install data exists in the optimization period (April 21–June 12, 2026).

**Efficiency analysis CAN proceed** comparing:
- **Baseline:** March 5 – April 12 (pre-install, 13,867 rows, 81.4 engine hours)
- **Post-install:** April 21 – June 12 (optimization, 20,696 rows)

**Limitations:**
- 8-day stabilization gap (April 13–20) not available — data should be pulled from ORBCOMM
- SkyView flow_lph and fuel_temp_delta_c fields are unreliable — use corrected IFM deltas
- CAN bus fuel data is the preferred primary source for efficiency calculations

**Recommendation:** Proceed with full before/after efficiency analysis using corrected IFM and CAN bus data from the Station 2007 archive files.
