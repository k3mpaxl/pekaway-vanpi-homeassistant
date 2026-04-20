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
- **Raspberry Pi 4 or 5** (with Pekaway VAN PI CORE board)

## Raspberry Pi Einrichtung

### 1. SSH-Zugang zum Host-System (Port 22222)

Home Assistant OS bietet zwei SSH-Zugänge:

| Port | Ziel |
|---|---|
| 22 | HA Docker-Container (Add-on Shell) |
| **22222** | **Host-System** (zum Editieren von `config.txt`) |

Um auf Port 22222 zugreifen zu können:

1. **SSH-Schlüsselpaar erstellen** — falls noch nicht vorhanden
   ([Windows/PuTTY](https://docs.digitalocean.com/products/droplets/how-to/add-ssh-keys/create-with-putty/),
   [Mac/Linux](https://docs.github.com/de/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent))
2. **USB-Stick vorbereiten**
   - FAT32 formatieren, Label **`CONFIG`** (case-sensitiv!)
   - Den **Public Key** als Textdatei `authorized_keys` (ohne Dateiendung) auf den Stick kopieren
3. **USB-Stick am Raspberry Pi einstecken**
4. **Add-on installieren**: Einstellungen → Add-ons → Add-on Store → **Advanced SSH & Web Terminal**
5. Im SSH Web Terminal ausführen:
   ```
   ha os import
   ```
6. **Home Assistant neu starten** (USB-Stick beim ersten Start stecken lassen, danach abziehen)
7. **Verbinden**:
   ```bash
   # Mac/Linux
   ssh root@homeassistant.local -p 22222
   # Windows: PuTTY → Port 22222, Private Key unter Connection → SSH → Auth
   ```

> **Deaktivieren**: Leeren USB-Stick mit dem Namen `CONFIG` einstecken und neu starten — der Zugang wird wieder deaktiviert.

### 2. `config.txt` anpassen

Per SSH (Port 22222) die Datei editieren:

```bash
vi /mnt/boot/config.txt
```

Am Ende folgende Zeilen hinzufügen:

```text
dtparam=i2c_vc=on
dtparam=i2c_arm=on
dtoverlay=w1-gpio
dtoverlay=uart1
dtoverlay=uart2
dtoverlay=uart4
dtoverlay=uart5
```

| Overlay | Zweck | Integration |
|---|---|---|
| `dtparam=i2c_vc=on` | VideoCore I²C | MCP23017, ADS1115, MPU-6050 |
| `dtparam=i2c_arm=on` | ARM I²C | MCP23017, ADS1115, MPU-6050 |
| `dtoverlay=w1-gpio` | 1-Wire Bus | 1-Wire SysBus (DS18B20) |
| `dtoverlay=uart1` | UART1 (RJ45) | — |
| `dtoverlay=uart2` | UART2 (RJ11 LIN) | — |
| `dtoverlay=uart4` | UART4 | Victron MPPT |
| `dtoverlay=uart5` | UART5 | Victron SmartShunt |

> **Achtung:** `dtoverlay=uart3` darf **nicht** angegeben werden — auf diesem Pin liegt der 1-Wire Temperatursensor!

### 3. I²C-Kernel-Modul laden

```bash
mkdir -p /mnt/boot/CONFIG/modules
echo "i2c-dev" > /mnt/boot/CONFIG/modules/rpi-i2c.conf
```

> **Hinweis:** Durch das Schreiben in `/mnt/boot/CONFIG/` kann es sein, dass der SSH-Zugang nach dem Reboot zurückgesetzt wird. In diesem Fall den USB-Stick-Schritt wiederholen.

### 4. Neustart

```bash
reboot
```

Am besten über **Entwicklerwerkzeuge → Konfiguration prüfen → Neu starten → Home Assistant neu starten**, so wird sichergestellt, dass alle Konfigurationen gültig sind.

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
