# CTS700 site HA migration: dual-path (YAML + Nilan integration)

Site: `homeassistant.newstargeted.com` / `homeassistant.local`  
Unit: CTS700 Nordic, `192.168.50.105:502`, unit id **1**

## Current state (01/09/2026)

| Component | Version / status |
|-----------|------------------|
| Modbus (YAML) | 61 entities, debug logging on |
| Nilan integration | v1.3.0 installed, **no config entries** |
| Fork target | **1.3.13** (PR #4 merged locally) |

## Filter probe results

| Register | Value | Meaning |
|----------|-------|---------|
| 1326 | 165 | Inlet interval |
| 1328 | 155 | Inlet **remaining** (Overview "155 dager") |
| 1327 | 180 | Exhaust interval |
| 1329 | 0 | Exhaust **remaining** (Nilan CTS "0 dager igjen") |
| 20103 | 155 | Same as 1328 on this unit |

Days since inlet change: **165 − 155 = 10**

## Step 1: Upgrade Nilan via HACS

1. HACS → Custom repositories → `https://github.com/master3395/veista-nilan`
2. Update **Nilan** to **1.3.13** (after push to GitHub)
3. Restart Home Assistant
4. Do **not** add hub yet

## Step 2: Trim overlapping YAML Modbus

In your Modbus YAML include(s), **comment out** sensors/climates that duplicate integration entities:

- Room setpoint / climate on **4746** (if covered by integration)
- Filter sensors on wrong registers
- Fan/temp/humidity/DHW sensors the integration will provide

**Keep in YAML:**

- CTS Log selftest writes (**20148**, **20181**, etc.)
- Event log dump (**10000+**)
- Custom automations not in integration map

See [`cts700-site-register-matrix.md`](cts700-site-register-matrix.md) overlap table.

## Step 3: Add Nilan config entry

1. Settings → Integrations → Nilan → **Legg til hub**
2. TCP, host `192.168.50.105`, port `502`, unit id `1`
3. Board: **CTS700 Compact P Nordic XL** (or auto-detect)
4. Setup probe runs once; `dead_registers` saved in config entry

## Step 4: Dashboard entity mapping

| Dashboard card | Prefer after migration |
|----------------|------------------------|
| Filter inn (Overview) | `sensor.nilan_days_to_inlet_filter_change` or remaining inlet |
| Fraluftsfilter (Nilan CTS) | `sensor.nilan_days_to_exhaust_filter_change` |
| Days since (optional) | `sensor.nilan_days_since_inlet_filter_change` |
| Room setpoint | Integration climate / `sensor.nilan_user_temperature_setpoint` |
| Fan | Integration fan entities (4699/4700 or 4747 step) |

## Step 5: Validate

1. Reload Modbus + confirm Nilan entry healthy
2. Compare filter values to CTS700 HMI
3. Run CTS Log selftest; no FAIL on filter/DHW
4. System log: no repeating Modbus ERROR for removed YAML addresses
5. Settings → Integrations → Nilan → Configure: confirm `dead_registers` in entry data

## YAML-only filter fix (if not adding integration yet)

```yaml
# Include from modbus_yaml/cts700_nordic_xl.yaml (1326-1329 sensors)
# Then:
# !include modbus_yaml/nilan_filter_templates.yaml
```
