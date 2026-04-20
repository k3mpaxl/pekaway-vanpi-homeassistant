# Pekaway VAN PI CORE — Home Assistant Integrations

[![HACS Custom](https://img.shields.io/badge/HACS-Custom-orange.svg)](https://hacs.xyz)
[![Home Assistant](https://img.shields.io/badge/Home%20Assistant-%E2%89%A52024.10-blue)](https://www.home-assistant.io/)

Custom Home Assistant integrations for the **Pekaway VAN PI CORE** board and related camper van hardware. Each integration lives in its own repository for clean HACS installation.

## Integrations

| Integration | Domain | Hardware | Repository |
|---|---|---|---|
| **Autoterm Air 2D** | `autoterm` | Autoterm Air 2D parking heater (USB serial) | [pekaway-ha-autoterm](https://github.com/k3mpaxl/pekaway-ha-autoterm) |
| **ADS1115 Water Level** | `ads_waterlevel` | ADS1115 4-channel ADC — tank level sensors | [pekaway-ha-ads-waterlevel](https://github.com/k3mpaxl/pekaway-ha-ads-waterlevel) |
| **MCP23017 I/O Expander** | `mcp23017` | MCP23017 — 8 inputs + 8 relay outputs | [pekaway-ha-mcp23017](https://github.com/k3mpaxl/pekaway-ha-mcp23017) |
| **MPU-6050 Tilt Sensor** | `mpu650` | MPU-6050 accelerometer — X/Y tilt angles | [pekaway-ha-mpu650](https://github.com/k3mpaxl/pekaway-ha-mpu650) |
| **1-Wire SysBus** | `onewire_sysbus` | DS18B20 temperature sensors via `/sys/bus/w1` | [pekaway-ha-onewire-sysbus](https://github.com/k3mpaxl/pekaway-ha-onewire-sysbus) |
| **Victron VE.Direct** | `victron_vedirect` | Victron MPPT + SmartShunt via VE.Direct UART | [pekaway-ha-victron-vedirect](https://github.com/k3mpaxl/pekaway-ha-victron-vedirect) |

## Installation

Each integration is installed individually via HACS:

1. **HACS** → **Integrations** → ⋮ → **Custom repositories**
2. Add the repository URL from the table above → Category: **Integration**
3. Install the integration and restart Home Assistant.
4. **Settings → Devices & Services → + Add Integration** → search for the integration name.

## System Requirements

- **Home Assistant** ≥ 2024.10.0
- **Python** ≥ 3.12
- **Raspberry Pi 4 or 5**

### Raspberry Pi `config.txt` overlays

```text
dtparam=i2c_arm=on
dtoverlay=w1-gpio
dtoverlay=uart4
dtoverlay=uart5
```

> **Note:** UART3 must NOT be enabled — the 1-Wire temperature sensor uses that pin.

See each integration's README for detailed setup instructions.

## Migration from v1.x

The older monolithic repository (`homeassistant_PekawayVANPICORE`) has been split into individual repos. The legacy YAML integrations `victron_mppt` and `victron_smartshunt` have been merged into **Victron VE.Direct** (`victron_vedirect`).

1. Remove old YAML configuration entries.
2. Uninstall the old integration from HACS.
3. Add the new individual repositories via HACS.
4. Set up each integration through the UI.

## Acknowledgements

This project is a fork of [maxlin1/homeassistant_PekawayVANPICORE](https://github.com/maxlin1/homeassistant_PekawayVANPICORE). Thanks to [maxlin1](https://github.com/maxlin1) for the original development.

`mcp23017` is based on the work of [jpcornil-git](https://github.com/jpcornil-git/HA-mcp23017).
`onewire_sysbus` is based on the work of [thecode](https://github.com/thecode/ha-onewire-sysbus).

## License

MIT — see [LICENSE](./LICENSE).
