---
name: esp32-lcd-dev
description: Board reference and development guide for the Waveshare ESP32-S3-Touch-LCD-4.3B
model-invoked: true
triggers:
  - ESP32-S3-Touch-LCD-4.3B
  - Waveshare 4.3 LCD
  - Waveshare ESP32
  - LVGL on ESP32
  - PlatformIO ESP32-S3
  - ST7262
  - GT911
  - CH422G
---

# Waveshare ESP32-S3-Touch-LCD-4.3B — Board Reference

You are working with the **Waveshare ESP32-S3-Touch-LCD-4.3B** board. This skill provides comprehensive hardware specs, GPIO pinout, and PlatformIO project setup so you can develop without searching the web.

## Quick Facts

| Feature | Detail |
|---|---|
| MCU | ESP32-S3-WROOM-1 (dual-core Xtensa LX7, 240 MHz) |
| PSRAM | 8 MB OPI (Octal SPI, 80 MHz) |
| Flash | 8 MB QIO |
| Display | 4.3" 800×480 RGB LCD, ST7262 controller, RGB565 |
| Touch | GT911 capacitive, I2C (SDA=GPIO8, SCL=GPIO9) |
| IO Expander | CH422G at I2C 0x20 (backlight, reset, SD) |
| USB | USB-C with native CDC serial |

## Reference Files

For detailed information, read these reference files:

- **[Hardware Specs](references/hardware-specs.md)** — Full hardware reference: display timing, touch config, IO expander, PSRAM, USB, init sequences
- **[GPIO Pinout](references/gpio-pinout.md)** — Complete GPIO pin map with occupied/free summary
- **[PlatformIO Setup](references/platformio-setup.md)** — Ready-to-use `platformio.ini`, `lv_conf.h`, and `main.cpp` skeleton

## Key Reminders

- The LCD uses `drawBitmap(x, y, width, height, data)` — **not** `(x1, y1, x2, y2, data)`
- Backlight is on/off only (no PWM) via CH422G pin 2
- Touch and IO expander share the same I2C bus (GPIO8/GPIO9)
- Always use `BOARD_WAVESHARE_ESP32_S3_TOUCH_LCD_4_3_B` build flag — it auto-configures the ESP32_Display_Panel library
- LVGL must run on a dedicated FreeRTOS task with a recursive mutex for thread safety
