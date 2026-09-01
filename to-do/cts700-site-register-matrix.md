# CTS700 site register probe matrix

Probed: 01/09/2026 23:43:47  
Host: 192.168.50.105:502 · Unit ID: 1

Single TCP session, sequential reads. Re-test with delay showed Modbus timeouts; values below from the first complete pass.

| Label | Type | Address | Status | Value / code |
|-------|------|---------|--------|--------------|
| filter_interval_inlet | holding | 1326 | ok | 165 |
| filter_interval_exhaust | holding | 1327 | ok | 180 |
| filter_remaining_inlet | holding | 1328 | ok | 155 |
| filter_remaining_exhaust | holding | 1329 | ok | 0 |
| filter_days_20103 | holding | 20103 | ok | 155 (matches 1328 remaining) |
| room_setpoint_4746 | holding | 4746 | ok | 200 (= 20.0 C) |
| room_setpoint_20102 | holding | 20102 | timeout | probe incomplete |
| fan_step_4747 | holding | 4747 | timeout | probe incomplete |
| fan_pct_21771 | holding | 21771 | timeout | probe incomplete |
| supply_fan_4699 | holding | 4699 | timeout | probe incomplete |
| extract_fan_4700 | holding | 4700 | timeout | probe incomplete |
| t4_20288 | holding | 20288 | timeout | probe incomplete |
| t5_20290 | holding | 20290 | timeout | probe incomplete |
| t6_20292 | holding | 20292 | timeout | probe incomplete |
| t8_20296 | holding | 20296 | timeout | probe incomplete |
| t4_5155 | holding | 5155 | timeout | probe incomplete |
| t5_5156 | holding | 5156 | timeout | probe incomplete |
| t6_5157 | holding | 5157 | timeout | probe incomplete |
| t8_5159 | input | 5159 | timeout | probe incomplete |
| dhw_setpoint_5548 | holding | 5548 | timeout | probe incomplete |
| dhw_setpoint_20460 | holding | 20460 | timeout | probe incomplete |
| dhw_top_5162 | input | 5162 | timeout | probe incomplete |
| humidity_4716 | input | 4716 | timeout | probe incomplete |
| avg_humidity_20164 | holding | 20164 | timeout | probe incomplete |
| op_mode_5432 | holding | 5432 | timeout | probe incomplete |

## Filter semantics (verified)

| Metric | Calculation | Value |
|--------|-------------|-------|
| Inlet days remaining | 1328 | **155** |
| Inlet days since change | 1326 - 1328 | **10** |
| Exhaust days remaining | 1329 | **0** |
| Exhaust days since change | 1327 - 1329 | **180** |

This matches dashboard text: Overview **Filter inn: 155 dager** (remaining), Nilan CTS **Fraluftsfilter: 0 dager igjen** (exhaust remaining).

## HA Modbus entity map (from `/local/nilan_cts_modbus_log.txt`)

| Entity (examples) | Register | Role |
|-------------------|----------|------|
| sensor.room_setpoint_cts | 4746 (recent), 20102 (legacy tests) | Room setpoint |
| sensor.fan_speed_level | 20148 | Fan percent writes |
| switch.high_fan_when_cooling | 20181 | High fan when cooling |
| switch.ventilation_mode | (integration/YAML) | Vent mode |
| switch.filter_in_reset / filter_out_reset | pulse | Maintenance |
| DHW climate | 20460 (risk: temp vs setpoint) | See Aug selftest SKIP |

## Overlap with integration entity map (post PR #4)

After adding Nilan config entry (CTS700 Nordic), **remove YAML polls** for entities the integration provides:

- Room setpoint / climate (4746)
- Filter days (1326-1329 via integration sensors)
- Fan, temps, humidity, DHW (per probe + integration dead_registers)

**Keep in YAML:** CTS selftest writes (20148, 20181), event-log dump (10000+), custom automations on CTS Log dashboard.

## Recommended fallbacks

- Filter: prefer **1328/1329** + template days-since; **20103** mirrors 1328 on this unit but use 1326-1329 for inlet/exhaust split
- Room setpoint: **4746** confirmed live
- Re-probe 4699/4700, 5159, 5548 individually before removing YAML sensors (bulk probe timed out)
