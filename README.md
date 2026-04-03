# ESPHome Smart Meter Reader (IEC 62056-21)

A lightweight, highly optimized ESPHome project that uses an optical probe to read electricity data from a smart utility meter via the IEC 62056-21 protocol. This specific configuration was developed and optimized for **"Комунар" (Komunar) smart meters commonly deployed in Kharkiv, Ukraine**, but will work with other standard IEC 62056-21 meters that require strict baud rate management.

This project seamlessly integrates real-time energy usage and calculated power consumption into Home Assistant, InfluxDB, and Grafana.

## Features
* **Multi-Tariff Support:** Extracts Total Energy, Tariff 1 (Day), and Tariff 2 (Night) readings (kWh).
* **Grid Monitoring:** Pulls live Phase 1 Grid Voltage (V) and Current (A).
* **Real-Time Power Calculation:** Calculates live active power consumption (W) locally on the ESP32 using instantaneous Voltage and Amperage.
* **Watchdog Crash Prevention:** Hard-coded to the manufacturer's 2400 baud limit (specific to the Комунар meters) to bypass auto-negotiation loops and prevent single-core ESP32 crashes.
* **Network Optimized:** Runs silently in `INFO` mode to maintain a stable Wi-Fi connection even in weak signal areas (e.g., metal utility cabinets).

## Hardware Required
* **Microcontroller:** ESP32-C3 (e.g., `esp32-c3-devkitm-1`)
* **Utility Meter:** "Комунар" (Komunar) smart electricity meter (or equivalent IEC 62056-21 meter)
* **Sensor:** Standard IR Optical Probe (IEC 62056-21 compliant)
* **Power:** 5V USB power supply
* *(Note: If your meter is inside a metal Faraday cage, splice and extend the 3V3, GND, TX, and RX wires so the ESP32 can sit completely outside the metal box for Wi-Fi reception).*

## Wiring

| Optical Probe | ESP32-C3 Pin | Notes |
| :--- | :--- | :--- |
| VCC | 3V3 | Requires 3.3V, not 5V! |
| GND | GND | |
| TX | GPIO20 | *Note: This is software-swapped to RX in the YAML* |
| RX | GPIO21 | *Note: This is software-swapped to TX in the YAML* |

*If you receive a "No transmission from meter" error, your TX/RX pairs are likely mirrored. Simply swap the GPIO assignments in the `uart:` section of the configuration.*

## Installation

1. Install [ESPHome](https://esphome.io/).
2. Clone this repository or copy the `smart-meter.yaml` configuration.
3. Update the `wifi:` block with your local SSID and Password.
4. Compile and upload via USB for the first flash:
   ```bash
   esphome run smart-meter.yaml