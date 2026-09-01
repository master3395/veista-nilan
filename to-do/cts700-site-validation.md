# CTS700 site validation checklist (01/09/2026)

## Filter vs HMI (verified via live Modbus probe)

| Check | Expected | Probed | Pass |
|-------|----------|--------|------|
| Inlet remaining | Overview "155 dager" | 1328 = 155 | Yes |
| Exhaust remaining | Nilan CTS "0 dager igjen" | 1329 = 0 | Yes |
| Inlet days since | 1326 - 1328 | 165 - 155 = 10 | Yes |
| Exhaust days since | 1327 - 1329 | 180 - 0 = 180 | Yes |
| 20103 vs 1328 | Same remaining | both 155 | Yes |

Filter UI mismatch in issue #3 was **not** wrong data on this unit: cards show **remaining** days correctly for inlet (155) and exhaust (0). Optional improvement: add **days since** sensors (PR #4 / templates).

## Repo validation

| Check | Status |
|-------|--------|
| PR #4 merged to master | Yes (v1.3.13) |
| register_probe.py present | Yes |
| modbus_yaml 1326-1329 filter | Yes |
| manifest 1.3.13 | Yes |
| Pushed to GitHub | Yes |

## HA validation (operator steps remaining)

1. HACS: add custom repo `https://github.com/master3395/veista-nilan`, category Integration, update Nilan to 1.3.13
2. Trim overlapping YAML Modbus per `cts700-site-ha-migration.md`
3. Add Nilan config entry (192.168.50.105:502, CTS700 Nordic)
4. Repoint dashboards per `cts700-site-dashboard-entities.md`
5. Restart HA; 30 min log watch for Modbus ERROR spam
6. CTS Log selftest: no DHW 20460 SKIP after DHW uses 5548

## CTS Log (last custom log entry)

`01/09/2026 23:18:37` OK DHW Supplement Enable (maintenance cycle, not register error)
