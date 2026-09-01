# CTS700 site log audit (01/09/2026)

## System log (Home Assistant Core)

- Modbus **debug logging enabled** on the Modbus integration (Integrations → Modbus).
- Browser audit: log panel loaded; use **Søk i logger** with `modbus`, `pymodbus`, `nilan`, `Could not read` after migration.
- Pre-migration: recurring errors likely from YAML polling registers that timeout or are absent on hybrid firmware (issue #3 pattern).

## Custom Modbus log (`/local/nilan_cts_modbus_log.txt`)

- **2840 lines**, last entry 01/09/2026 23:18:37
- Recent entries: maintenance cycle `unavailable -> OK` on switches (Filter In/Out Reset, DHW, Ventilation Mode, etc.) every ~15 min (CTS Log event-log dump pauses hub briefly)
- **No pymodbus ERROR lines** in custom log (only OK/FAIL from selftest scripts)
- Known FAIL patterns (Aug 2026):
  - `DHW climate setpoint | SKIPPED: target_temp_register=20460 same as current temp sensor`
  - Fan percent via **20148** vs sensor lag

## Correlation with probe matrix

| Register | Probe | Risk if still in YAML poll |
|----------|-------|----------------------------|
| 1326-1329 | OK | None; use for filter UI |
| 20103 | OK (155) | Redundant with 1328 on this unit |
| 4746 | OK | Keep one path only after integration entry |
| 20102, 21771, 20288+ | timeout in bulk probe | Remove from YAML if integration probe marks dead |

## Post-migration target

- Zero repeating `pymodbus returned isError True` in Core log for 30 min
- Custom CTS selftest: no FAIL on filter/DHW setpoint
