
# externalSensorsTechnion

ESP32 firmware for environmental sensor monitoring and OTA firmware updates.

This project integrates multiple external environmental sensors connected to an ESP32 and periodically reports measurements to the Smartflow backend. The system also supports secure over-the-air (OTA) firmware updates via GitHub Pages.

The firmware is designed for rooftop deployments and includes stability features such as WiFi reconnect logic, sensor fault detection, filtering, and firmware update management.

---

# System Overview

The ESP32 reads data from three environmental sensors:

| Sensor | Type | Interface | Pin |
|------|------|------|------|
| Pyranometer | Solar radiation | 4-20mA | GPIO34 |
| TR-AM3W Wind Sensor | Wind speed | 4-20mA | GPIO35 |
| Rainwise Tipping Bucket | Rain gauge | Pulse input | GPIO27 |

Sensor readings are processed and transmitted periodically to the Smartflow cloud backend.

The firmware also supports **OTA firmware updates via GitHub** using a `version.json` file and firmware binary.

---

# Main Features

- Environmental sensor monitoring
- 4-20mA analog sensor interface
- Rain gauge pulse counting
- Sensor fault detection
- Signal filtering for stable measurements
- Automatic WiFi reconnection
- Cloud reporting
- GitHub-based OTA firmware updates
- Firmware version management
- Firmware archiving for rollback capability

---

# Hardware Setup

## ESP32 Pin Configuration

| Function | ESP32 Pin |
|------|------|
| Pyranometer current input | GPIO34 |
| Wind sensor current input | GPIO35 |
| Rain gauge pulse input | GPIO27 |

---

# 4-20mA Sensor Interface

Both the pyranometer and wind sensor output **4-20mA current loops**.

A **165Ω shunt resistor** converts the current into voltage readable by the ESP32 ADC.

### Electrical conversion

| Current | Voltage |
|------|------|
| 4mA | ~0.66V |
| 20mA | ~3.30V |

This maps well to the ESP32 ADC input range.

---

# Sensor Calculations

## Pyranometer

Solar irradiance is calculated using:

solarWm2 = (solar_mA - 4.0) * SOLAR_FACTOR

Default calibration value:

SOLAR_FACTOR = 125.0

### Calibration Procedure

1. Measure the sensor current output
2. Compare with a reference irradiance source
3. Adjust `SOLAR_FACTOR` until values match reference readings

---

## Wind Sensor (TR-AM3W)

Wind speed is calculated using:

windMps = (wind_mA - 4.0) * WIND_FACTOR

Default calibration value:

WIND_FACTOR = 3.125

### Calibration Procedure

1. Compare readings against a known wind reference
2. Adjust `WIND_FACTOR` if required

---

## Rain Gauge

The Rainwise tipping bucket generates a pulse for each bucket tip.

Default conversion:

mmPerTip = 0.254

Rainfall is calculated as:

rainMm = tips * mmPerTip

If a different bucket size is used, adjust the `mmPerTip` constant.

---

# Signal Filtering

To improve measurement stability in rooftop installations, the firmware uses:

### Oversampling

Multiple ADC readings are averaged to reduce noise.

### Exponential Moving Average (EMA)

filtered = prev + alpha * (value - prev)

Default:

EMA_ALPHA = 0.25

This smooths sensor readings without introducing large delays.

---

# Sensor Fault Detection

The firmware automatically detects sensor faults based on current levels.

| Condition | Meaning |
|------|------|
| < 3.8mA | Sensor disconnected |
| > 22mA | Short circuit or wiring fault |

Detected faults are reported in the `errors` field sent to the server.

Example errors:

SOLAR_DISCONNECTED
WIND_SHORT

---

# Data Transmission

Sensor data is sent to the backend collection:

externalSensorsData

Typical transmitted fields:

| Field | Description |
|------|------|
| title | rooftop name |
| version | firmware version |
| solarWm2 | solar irradiance |
| windMps | wind speed |
| rainMm | rainfall |
| solar_mA | raw sensor current |
| wind_mA | raw sensor current |
| solarFault | sensor fault flag |
| windFault | sensor fault flag |
| tips | rain gauge pulse count |
| mmPerTip | rain conversion factor |
| errors | fault message |

---

# Transmission Interval

The send interval is configurable in the firmware:

unsigned long externalSendInterval = 10000;

Default = **10 seconds**

---

# OTA Firmware Updates

Firmware updates are delivered via GitHub Pages.

Devices periodically check:

https://moti-smartflow-rws.github.io/externalSensorsTechnion/version.json

Example `version.json`:

{
  "version": "4.7",
  "url": "https://moti-smartflow-rws.github.io/externalSensorsTechnion/firmware_v4.7.bin"
}

If the version is newer than the currently running firmware, the ESP32:

1. Downloads the binary
2. Writes it to flash
3. Reboots automatically

---

# OTA Repository Structure

externalSensorsTechnion/

ESP32Code.ino
push_ota_firmware_clean.sh
version.json
firmware_vX.X.bin

old_firmware_versions/
archived firmware binaries

---

# OTA Release Process

The OTA update process is automated using the script:

push_ota_firmware_clean.sh

Workflow:

1. Compile firmware in Arduino IDE
2. Export compiled binary
3. Run OTA push script
4. Script automatically:
   - Detects firmware version
   - Archives old firmware
   - Copies new binary
   - Updates `version.json`
   - Pushes changes to GitHub

---

# Firmware Versioning

The firmware version is defined in the code:

String currentVersion = "4.7";

The OTA script reads this value automatically.

---

# Maintainer

Smartflow RWS  
Environmental monitoring and smart water management systems

https://smartflow-rws.com
