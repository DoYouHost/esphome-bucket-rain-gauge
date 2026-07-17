# Bucket Rain Gauge

ESPHome firmware for a **Seeed Studio XIAO ESP32-C3** driving a tipping-bucket
rain gauge, with onboard temperature, humidity, and pressure sensing. Rainfall
and weather are reported to Home Assistant over the native ESPHome API, with OTA
updates delivered through the DoYouHost `beta` and `production` channels.

## Installation

Open the project's GitHub Pages site and use the **Install** button (ESP Web
Tools) to flash the firmware over USB straight from your browser. After
flashing, onboard Wi-Fi with Improv (over Bluetooth or USB serial) and adopt the
device into your Home Assistant.

## Features

- **Rain detection** — 49E linear Hall sensor read on the ADC; each bucket tip
  toggles the `Bucket Position` state.
- **Rainfall totals** — lifetime `Total Rainfall`, plus `Today` / `This Week` /
  `This Month` / `This Year` (disabled by default; enable per device in Home
  Assistant). Period totals reset on Home Assistant's local time.
- **Rain rate** — 1-minute, 10-minute average, and hourly.
- **Weather** — AHT20 (temperature, humidity) and BMP280 (pressure) over I2C,
  with software smoothing.
- **Calibration at runtime** — `Rain Per Tip`, `Tip Threshold`, and
  `Tip On Falling Signal` controls; no reflash needed.
- **Onboarding** — Wi-Fi via Improv over BLE and Serial; BLE auto-disables
  shortly after Wi-Fi connects. The API encryption key is provisioned at
  runtime, so no key is baked into distributed firmware.

## Hardware & wiring

Target board: Seeed Studio XIAO ESP32-C3. Default pin assignments (all set via
`substitutions` in [`bucket-rain-gauge.yml`](bucket-rain-gauge.yml)):

| Signal        | XIAO pad | GPIO   | Notes                              |
| ------------- | -------- | ------ | ---------------------------------- |
| Hall analog   | A1 / D1  | GPIO3  | ADC1 channel (Wi-Fi safe)          |
| I2C SDA       | D4       | GPIO6  | AHT20 `0x38`, BMP280 `0x77`        |
| I2C SCL       | D5       | GPIO7  | needs pull-ups if not on the board |

Power the 49E Hall sensor from **3V3** (not 5V) so its output stays within the
ESP32-C3's 0–3.3 V ADC input range.

## Credits

The tipping-bucket rain gauge hardware design is based on
[Rain Gauge by MakerWorld](https://makerworld.com/en/models/1366151-rain-gauge#profileId-1411849).
