# About

Bucket Rain Gauge is an ESPHome firmware for a Seeed Studio XIAO ESP32-C3
driving a tipping-bucket rain gauge, with onboard temperature, humidity, and
pressure sensing. It reports to Home Assistant over the native ESPHome API and
supports OTA updates through the DoYouHost beta and production channels.

Measurements include rainfall totals (lifetime and per day / week / month /
year), rain rate (1-minute, 10-minute average, and hourly), and weather from an
AHT20 + BMP280 sensor. Rain detection uses a 49E Hall sensor with runtime
calibration, so no reflashing is needed to tune it.

After flashing, onboard Wi-Fi with Improv (over Bluetooth or USB serial) and
adopt the device into your own Home Assistant — local time and timezone come
from HA automatically.

# Installation

You can use the button below to install the pre-built firmware directly to your device via USB from the browser.

<esp-web-install-button manifest="firmware/manifest.json"></esp-web-install-button>

<script type="module" src="https://unpkg.com/esp-web-tools@10/dist/web/install-button.js?module"></script>
