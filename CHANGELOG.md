# 2026.7
## .0
- Initial release of the Bucket Rain Gauge firmware for the Seeed Studio XIAO
  ESP32-C3.
- Tipping-bucket rain detection via a 49E linear Hall sensor read on the ADC,
  with a tunable threshold, direction, and per-tip volume for calibration.
- Rainfall measurements: lifetime total, per-period totals (today / week /
  month / year, disabled by default), and rain rate (1 min, 10 min average,
  hourly). Period totals reset on Home Assistant local time.
- Environmental sensing over I2C: AHT20 (temperature, humidity) and BMP280
  (pressure), with software smoothing.
- Wi-Fi onboarding via Improv over BLE and Serial; BLE is disabled shortly
  after Wi-Fi connects to free the radio.
- Native API encryption key provisioned at runtime (no key baked into the
  firmware); OTA updates through the DoYouHost beta and production channels.
