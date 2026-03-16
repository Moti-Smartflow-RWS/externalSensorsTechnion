<<<<<<< HEAD
# ESP32 T-Relay Firmware  
Advanced SmartFlow Valve Controller

## Overview
This repository contains the full firmware for the **SmartFlow ESP32 T‑Relay** device.  
The firmware supports 4‑wire actuator control (OPEN / CLOSE), dual‑limit‑switch feedback, water‑level sensing, BME280 environmental data, and full OTA update capability.

The project is designed for Smartflow‑rws stormwater and flood‑prevention systems.

---

## Features
### 🔧 Valve Control (T-Relay Board)
- Two‑relay actuator control (OPEN relay, CLOSE relay)
- Limit switches:
  - **GPIO32 LOW → valve fully OPEN**
  - **GPIO33 LOW → valve fully CLOSED**
  - **Both HIGH → valve MOVING**
- Partial‑position control using calibrated travel times
- Real‑time protection against conflicting relay activation

### 📏 Automatic Calibration
- Command: `calibration`
- Device fully opens, waits 5 seconds, then:
  - Measures **FULL CLOSE** time using LS feedback  
  - Measures **FULL FULL OPEN** time  
- Prints result to serial monitor
- Used for all future `set XX` movement commands

---

## 📡 Connectivity
- WiFi auto‑reconnection (non‑blocking)
- Secure HTTPS GET / POST / PUT to SmartFlow backend
- Device name stored in NVS (`Smartflow_Wifi_XX`)
- Supports OTA update from GitHub Releases

---

## 🌡 Sensors
- **BME280** (temperature, humidity, pressure)
- **Water‑level sensor**:
  - Oversampling  
  - EMA filtering  
  - Deadband + hysteresis  
  - User calibration at **0 cm** and **10 cm**

---

## 🧪 Serial Commands
| Command | Description |
|--------|-------------|
| `calibration` | Fully open → wait → measure open/close times |
| `set <0..100>` | Move valve to % open/close |
| `setopenms <ms>` | Manually set FULL_OPEN_MS |
| `setclosems <ms>` | Manually set FULL_CLOSE_MS |
| `getcal` | Print open/close timing calibration |
| `status` | Print valve LS + relay state |
| `wlcal` | Show water‑level calibration instructions |
| `wlcal 0` | Capture ADC reading at 0 cm |
| `wlcal 10` | Capture ADC reading at 10 cm |
| `wlcal show` | Show current values |
| `wlcal reset` | Reset WL calibration |

---

## 📂 Repository Structure
```
esp32-T-Relay/
├── firmware/
│   ├── src/
│   │   └── main.cpp
│   ├── secrets/
│   │   └── arduino_secrets.h
│   └── smartflow_config.h
├── ota/
│   ├── version.json
│   └── firmware.bin
├── scripts/
│   ├── push_ota_firmware.sh
│   ├── push_github.sh
│   └── build_firmware.sh
└── README.md
```

---

## 🚀 OTA Update Flow
1. Build firmware → produce `.bin`
2. Update `ota/version.json`:
```
{
  "version": "4.3",
  "url": "https://your-github-repo/firmware.bin"
}
```
3. Push to GitHub  
4. Devices check version every 60 seconds  
5. If newer → auto-download + install

---

## 📝 Notes
- Never activate OPEN and CLOSE relays together.
- Limit switches are authoritative for end‑positions.
- Calibration must run at least once after flashing.
- Water‑level calibration greatly improves accuracy.

---

## 📧 Support
For assistance contact: **support@smartflow-rws.com**  
SmartFlow website: **https://www.smartflow-rws.com**

---

Enjoy your new clean repository 🚀
=======
# externalSensorsTechnion
Data integration of external Sensors for Technion project
>>>>>>> 6304084adda7d74643f211d37f4d64d9ca4e5f51
