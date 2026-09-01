# CTS700 site dashboard entity mapping (post 1.3.13)

Use after Nilan config entry is added and overlapping YAML polls are trimmed.

## Nilan CTS tab

| Card / text | Current (YAML/custom) | After migration |
|-------------|----------------------|-----------------|
| Fraluftsfilter: X dager igjen | Custom template or YAML sensor | `sensor.nilan_days_to_exhaust_filter_change` |
| Filterinfo inlet | Overview cross-ref | `sensor.nilan_days_to_inlet_filter_change` |
| Viftehastighet | `sensor.fan_speed_level` / fan entities | Integration `sensor.nilan_fan_speed_percent` or supply/extract 4699/4700 |
| Rom-setpunkt | `sensor.room_setpoint_cts` (4746) | `climate.nilan_*` or `sensor.nilan_user_temperature_setpoint` |

## Overview tab

| Card | After migration |
|------|-----------------|
| Filter inn: 155 dager | `sensor.nilan_days_to_inlet_filter_change` (remaining) |
| Nilan CTS700 status block | Keep; update entity refs to integration |
| Systemmodus / Bypass | Integration operation mode / bypass entities if exposed |

## Optional new cards (PR #4)

- `sensor.nilan_days_since_inlet_filter_change` (interval − remaining)
- `sensor.nilan_days_since_exhaust_filter_change`
- `sensor.nilan_filter_interval_inlet` / `_exhaust` (diagnostics)

## CTS Log tab

Keep custom **Kjør** scripts and file log links. Update verify targets in automations/scripts from YAML entity IDs to integration IDs where duplicated.

Example replacements:

- `sensor.room_setpoint_cts` → integration room setpoint sensor
- Filter verify → `sensor.nilan_days_to_inlet_filter_change`

Do **not** remove CTS Log selftest write paths (20148, 20181) until integration exposes equivalent write helpers.
