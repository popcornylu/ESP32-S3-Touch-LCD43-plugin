# Hardware Specifications — Waveshare ESP32-S3-Touch-LCD-4.3B

## Board Overview

- **Module**: ESP32-S3-WROOM-1 (dual-core Xtensa LX7 @ 240 MHz)
- **PSRAM**: 8 MB OPI (Octal SPI), 80 MHz, XIP enabled, 64-byte cache line
- **Flash**: 8 MB QIO, 80 MHz
- **USB**: USB-C with native USB CDC serial
  - `ARDUINO_USB_MODE=1`
  - `ARDUINO_USB_CDC_ON_BOOT=1`

## Display

- **Size**: 4.3 inches
- **Resolution**: 800 × 480 pixels
- **Interface**: RGB (parallel)
- **Controller**: ST7262
- **Color depth**: RGB565 (16-bit)
- **Color byte swap**: Not needed (`LV_COLOR_16_SWAP=0`)

### LCD Timing Parameters

| Parameter | Value |
|---|---|
| Pixel Clock (PCLK) | 16 MHz |
| Horizontal Pulse Width (HPW) | 4 |
| Horizontal Back Porch (HBP) | 8 |
| Horizontal Front Porch (HFP) | 8 |
| Vertical Pulse Width (VPW) | 4 |
| Vertical Back Porch (VBP) | 8 |
| Vertical Front Porch (VFP) | 8 |
| Bounce Buffer Size | 8000 |

### LCD drawBitmap API

**IMPORTANT**: The API signature is:
```cpp
drawBitmap(x, y, width, height, data)
```
**NOT** `drawBitmap(x1, y1, x2, y2, data)`. The 3rd and 4th parameters are width and height, not coordinates.

## Touch Panel

- **Controller**: GT911 (Goodix)
- **Interface**: I2C at 400 kHz
- **I2C Address**: 0x5D (default; can be 0x14 depending on reset sequence)
- **Pins**: SDA = GPIO8, SCL = GPIO9, INT = GPIO4
- **Type**: Capacitive, multi-touch capable

### Touch Address Selection

The GT911 I2C address is determined during reset:
- INT pin LOW during reset → address 0x5D
- INT pin HIGH during reset → address 0x14
The ESP32_Display_Panel library handles this automatically when using the board define.

## IO Expander — CH422G

- **Interface**: I2C at address 0x20
- **I2C Bus**: Shared with touch panel (SDA = GPIO8, SCL = GPIO9)
- **Library**: ESP32_IO_Expander v1.1.0

### CH422G Pin Assignments

| Pin | Function | Notes |
|---|---|---|
| 0 | LCD Reset | Active low |
| 1 | Touch Reset | Active low |
| 2 | Backlight | Active high, on/off only (no PWM) |
| 3 | Available | Free for user |
| 4 | Available | Free for user |
| 5 | Available | Free for user |
| 6 | Available | Free for user |
| 7 | Available | Free for user |

### Init Sequence

The ESP32_Display_Panel library handles initialization automatically when `BOARD_WAVESHARE_ESP32_S3_TOUCH_LCD_4_3_B` is defined. The sequence is:

1. IO expander (CH422G) initialization on I2C bus
2. LCD reset via IO expander pin 0 (pulse low, then high)
3. Touch reset via IO expander pin 1 (with INT pin control for address selection)
4. Backlight enable via IO expander pin 2

## PSRAM Configuration

- **Mode**: OPI (Octal SPI)
- **Speed**: 80 MHz
- **Memory type build flag**: `board_build.arduino.memory_type = qio_opi`
- **Build flag**: `-DBOARD_HAS_PSRAM`
- **XIP**: Enabled (execute in place)
- **Cache line**: 64 bytes

### PSRAM Usage Notes

- Large buffers (display frame buffers, image data) should use PSRAM via `heap_caps_malloc(size, MALLOC_CAP_SPIRAM)`
- LVGL draw buffers work best in internal RAM for DMA: `heap_caps_calloc(1, size, MALLOC_CAP_INTERNAL)`
- The recommended LVGL buffer size is `800 * 20 * sizeof(lv_color_t)` (one 20-line strip)
