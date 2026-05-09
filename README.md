# Tater-S3Box-Display

ESPHome firmware for an ESP32-S3-BOX-3 style display that talks to Tater directly instead of binding the screen to Home Assistant widgets.

The screen is built with LVGL and uses the Tater color language: near-black surfaces, warm off-white text, muted secondary text, and burnt orange accents.

## What It Shows

- A Tater home dashboard with time, date, connection status, indoor/outdoor temperature, humidity, wind, rain, and lightning slots.
- A transient notification page for display events pushed through Tater.
- A dedicated tool-call page for live voice tool progress, using the mirrored-dot animation language from the Tater voice firmware.

## Tater API

The firmware polls these Tater endpoints:

```text
GET /tater-ha/v1/display/feed
GET /tater-ha/v1/display/events
```

If Tater voice/display API auth is enabled, set `tater_token` to the same token used by the ESPHome voice routes.

## Setup

Update the substitutions at the top of [s3box.yaml](s3box.yaml):

```yaml
substitutions:
  device_name: taters3box
  friendly_name: taterS3Box
  display_target: livingroom
  tater_base_url: http://tater.local:8501
  tater_token: ""
  wifi_ssid: !secret wifi_ssid
  wifi_password: !secret wifi_password
  device_ip: 10.4.20.237
```

Then adjust the feed slot entity IDs if needed:

```yaml
sensor_temp_out: sensor.weather_outdoor_temperature
sensor_temp_in: sensor.living_room_temperature
sensor_humidity_out: sensor.humidity
sensor_humidity_in: sensor.up_sense_humidity_level
sensor_wind_speed: sensor.wind_speed
sensor_rain_rate: sensor.rain_rate
sensor_lightning_strikes: sensor.weather_lightning_strikes
```

## Requirements

- ESPHome 2026.4.0 or newer
- ESP32-S3 Box class device with PSRAM
- Tater running the display feed/event API

## Notes

- The display uses LVGL with ESPHome's `mipi_spi` S3BOX display driver, `auto_clear_enabled: false`, and `update_interval: never`.
- Tater polling waits for Wi-Fi and uses single-run scripts so a missing network or offline Tater API cannot overlap HTTP requests during boot.
- Fonts use ESPHome's Google Fonts support so the Tater firmware builder can compile the fetched YAML without extra local assets.
- Camera snapshot rendering is the next layer to add. The Tater event API already carries image URLs; the firmware currently renders the event text first.
