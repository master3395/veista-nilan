## CTS700 site probe + dual-path resolution

Validated on live CTS700 Nordic at `192.168.50.105:502` (unit 1).

### Filter registers (verified)

| Register | Value | Meaning |
|----------|-------|---------|
| 1326 | 165 | Inlet interval |
| 1328 | 155 | Inlet remaining (matches Overview card) |
| 1327 | 180 | Exhaust interval |
| 1329 | 0 | Exhaust remaining (matches "0 dager igjen") |
| 20103 | 155 | Mirrors 1328 on this firmware |

Days since inlet change: **165 - 155 = 10**.

### Repo changes

- **PR #4 merged** to `master` (v1.3.13): setup-time register probe, Nordic inlet/exhaust filter entities, `dead_registers` persistence
- **modbus_yaml/** synced: 1326-1329 filter sensors, DHW 5548 primary, dual-path README
- Probe script: `to-do/probe_registers.ps1`
- HA migration guide: `to-do/cts700-site-ha-migration.md`

### HA site (YAML + integration)

Current site uses **YAML Modbus (61 entities)** plus Nilan integration v1.3.0 (no entry yet). Plan: upgrade to 1.3.13 via HACS, trim overlapping YAML polls, add config entry so integration probe handles dead 20xxx on units that lack them.

This unit has **both** 1326-1329 and live 20103; exhaust at 0 days remaining is correct per 1329.
