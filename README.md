# ESP32-S3-Touch-LCD43 Plugin for Claude Code

A Claude Code plugin providing comprehensive hardware reference and development tools for the **Waveshare ESP32-S3-Touch-LCD-4.3B** board.

## What's Included

### Model-Invoked Skill: `esp32-lcd-dev`

Automatically activates when working on ESP32-S3-Touch-LCD-4.3B projects. Provides:

- **Hardware specs** — Display timing, touch config, IO expander, PSRAM, USB
- **GPIO pinout** — Complete pin map with occupied/free summary
- **PlatformIO setup** — Ready-to-use `platformio.ini`, `lv_conf.h`, and `main.cpp` skeleton

### User-Invoked Skill: `/build`

Quick PlatformIO build commands:

```
/build          # Compile
/build upload   # Compile and upload
/build monitor  # Serial monitor
/build clean    # Clean build artifacts
```

## Installation

Use the `--plugin-dir` flag when launching Claude Code:

```bash
claude --plugin-dir path/to/ESP32-S3-Touch-LCD43-plugin
```

## Hardware Overview

| Feature | Detail |
|---|---|
| MCU | ESP32-S3 (dual-core LX7, 240 MHz) |
| PSRAM | 8 MB OPI |
| Flash | 8 MB QIO |
| Display | 4.3" 800×480 RGB LCD (ST7262) |
| Touch | GT911 capacitive (I2C) |
| IO Expander | CH422G (backlight, reset) |
| USB | USB-C native CDC serial |
