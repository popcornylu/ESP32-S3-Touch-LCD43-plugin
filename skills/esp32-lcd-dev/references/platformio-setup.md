# PlatformIO Project Setup — Waveshare ESP32-S3-Touch-LCD-4.3B

## platformio.ini

```ini
[env:waveshare-lcd-43b]
platform = https://github.com/pioarduino/platform-espressif32/releases/download/53.03.11/platform-espressif32.zip
platform_packages =
    platformio/framework-arduinoespressif32@https://github.com/espressif/arduino-esp32.git#3.1.1
    platformio/framework-arduinoespressif32-libs@https://dl.espressif.com/AE/esp-arduino-libs/esp32-3.1.1-h.zip
framework = arduino
board = esp32-s3-devkitc-1
monitor_speed = 115200

; Memory configuration
board_build.arduino.memory_type = qio_opi
board_build.f_flash = 80000000L
board_build.flash_mode = qio

build_flags =
    -DBOARD_HAS_PSRAM
    -DARDUINO_USB_MODE=1
    -DARDUINO_USB_CDC_ON_BOOT=1
    -DCORE_DEBUG_LEVEL=1
    ; LVGL
    -DLV_CONF_INCLUDE_SIMPLE
    -DLV_LVGL_H_INCLUDE_SIMPLE
    -DLV_COLOR_16_SWAP=0
    ; Waveshare board auto-config for ESP32_Display_Panel
    -DESP_PANEL_BOARD_DEFAULT_USE_SUPPORTED=1
    -DBOARD_WAVESHARE_ESP32_S3_TOUCH_LCD_4_3_B
    ; Include src/ so lv_conf.h is found
    -I src

lib_deps =
    https://github.com/esp-arduino-libs/ESP32_Display_Panel.git
    https://github.com/esp-arduino-libs/ESP32_IO_Expander.git#v1.1.0
    https://github.com/esp-arduino-libs/esp-lib-utils.git#v0.2.0
    https://github.com/lvgl/lvgl.git#v8.4.0
```

### Build Flags Explained

| Flag | Purpose |
|---|---|
| `BOARD_HAS_PSRAM` | Enables 8 MB OPI PSRAM |
| `ARDUINO_USB_MODE=1` | Use native USB (not JTAG) |
| `ARDUINO_USB_CDC_ON_BOOT=1` | USB CDC serial on boot |
| `LV_CONF_INCLUDE_SIMPLE` | LVGL looks for `lv_conf.h` via include path |
| `LV_LVGL_H_INCLUDE_SIMPLE` | Use `#include <lvgl.h>` instead of path-based include |
| `LV_COLOR_16_SWAP=0` | No byte swap for RGB565 (RGB interface doesn't need it) |
| `ESP_PANEL_BOARD_DEFAULT_USE_SUPPORTED=1` | Enable built-in board definitions |
| `BOARD_WAVESHARE_ESP32_S3_TOUCH_LCD_4_3_B` | Select this specific board config |
| `-I src` | Add `src/` to include path for `lv_conf.h` |

### Library Dependencies

| Library | Version | Purpose |
|---|---|---|
| ESP32_Display_Panel | latest | Display driver with built-in Waveshare board support |
| ESP32_IO_Expander | v1.1.0 | CH422G IO expander driver |
| esp-lib-utils | v0.2.0 | Utility library required by display panel |
| LVGL | v8.4.0 | Graphics library |

## Minimal lv_conf.h

Place this in `src/lv_conf.h`:

```c
#ifndef LV_CONF_H
#define LV_CONF_H

#include <stdint.h>

/* Color settings */
#define LV_COLOR_DEPTH 16
#define LV_COLOR_16_SWAP 0

/* Memory — use stdlib malloc (works well with PSRAM) */
#define LV_MEM_CUSTOM 1
#if LV_MEM_CUSTOM
#define LV_MEM_CUSTOM_INCLUDE <stdlib.h>
#define LV_MEM_CUSTOM_ALLOC malloc
#define LV_MEM_CUSTOM_FREE free
#define LV_MEM_CUSTOM_REALLOC realloc
#endif

/* Tick — use Arduino millis() */
#define LV_TICK_CUSTOM 1
#if LV_TICK_CUSTOM
#define LV_TICK_CUSTOM_INCLUDE "Arduino.h"
#define LV_TICK_CUSTOM_SYS_TIME_EXPR (millis())
#endif

/* Display */
#define LV_DPI_DEF 130

/* Logging */
#define LV_USE_LOG 0

/* Fonts */
#define LV_FONT_MONTSERRAT_14 1
#define LV_FONT_MONTSERRAT_24 1
#define LV_FONT_MONTSERRAT_48 1
#define LV_FONT_DEFAULT &lv_font_montserrat_14

/* Core widgets */
#define LV_USE_ARC        1
#define LV_USE_BAR        1
#define LV_USE_BTN        1
#define LV_USE_BTNMATRIX  1
#define LV_USE_CANVAS     1
#define LV_USE_CHECKBOX   1
#define LV_USE_DROPDOWN   1
#define LV_USE_IMG        1
#define LV_USE_LABEL      1
#define LV_USE_LINE       1
#define LV_USE_ROLLER     1
#define LV_USE_SLIDER     1
#define LV_USE_SWITCH     1
#define LV_USE_TEXTAREA   1
#define LV_USE_TABLE      1

/* Extra widgets — enable as needed */
#define LV_USE_ANIMIMG    0
#define LV_USE_CALENDAR   0
#define LV_USE_CHART      0
#define LV_USE_COLORWHEEL 0
#define LV_USE_IMGBTN     0
#define LV_USE_KEYBOARD   0
#define LV_USE_LED        0
#define LV_USE_LIST       0
#define LV_USE_MENU       0
#define LV_USE_METER      0
#define LV_USE_MSGBOX     0
#define LV_USE_SPAN       0
#define LV_USE_SPINBOX    0
#define LV_USE_SPINNER    0
#define LV_USE_TABVIEW    0
#define LV_USE_TILEVIEW   0
#define LV_USE_WIN        0

/* Themes */
#define LV_USE_THEME_DEFAULT 1
#define LV_THEME_DEFAULT_DARK 1

/* Snapshot (optional — useful for screenshots) */
#define LV_USE_SNAPSHOT 1

#endif /* LV_CONF_H */
```

## Minimal main.cpp Skeleton

```cpp
#include <Arduino.h>
#include <lvgl.h>
#include <ESP_Panel_Library.h>

using namespace esp_panel::board;
using namespace esp_panel::drivers;

/* LVGL buffer: 800px wide × 20 lines */
#define LVGL_BUF_SIZE (800 * 20)

static Board *panel = NULL;
static SemaphoreHandle_t lvgl_mux = NULL;

/* ---- LVGL display flush callback ---- */
static void lvgl_flush_cb(lv_disp_drv_t *disp, const lv_area_t *area, lv_color_t *color_p)
{
    /* IMPORTANT: params are (x, y, width, height, data) — NOT (x1, y1, x2, y2, data) */
    panel->getLCD()->drawBitmap(
        area->x1, area->y1,
        area->x2 - area->x1 + 1,
        area->y2 - area->y1 + 1,
        (const uint8_t *)color_p
    );
    lv_disp_flush_ready(disp);
}

/* ---- LVGL touch read callback ---- */
static void lvgl_touch_cb(lv_indev_drv_t *indev, lv_indev_data_t *data)
{
    TouchPoint point;
    int count = panel->getTouch()->readPoints(&point, 1, 0);
    if (count > 0) {
        data->state = LV_INDEV_STATE_PR;
        data->point.x = point.x;
        data->point.y = point.y;
    } else {
        data->state = LV_INDEV_STATE_REL;
    }
}

/* ---- LVGL FreeRTOS task ---- */
static void lvgl_task(void *arg)
{
    uint32_t delay_ms;
    while (1) {
        xSemaphoreTakeRecursive(lvgl_mux, portMAX_DELAY);
        delay_ms = lv_timer_handler();
        xSemaphoreGiveRecursive(lvgl_mux);
        if (delay_ms > 500) delay_ms = 500;
        if (delay_ms < 1) delay_ms = 1;
        vTaskDelay(pdMS_TO_TICKS(delay_ms));
    }
}

/* ---- Setup ---- */
void setup()
{
    Serial.begin(115200);

    /* Initialize board (LCD + touch + IO expander auto-configured) */
    panel = new Board();
    panel->init();
    panel->begin();

    /* Initialize LVGL */
    lv_init();

    /* Allocate draw buffer in internal RAM for best DMA performance */
    static lv_disp_draw_buf_t draw_buf;
    uint8_t *buf = (uint8_t *)heap_caps_calloc(1, LVGL_BUF_SIZE * sizeof(lv_color_t), MALLOC_CAP_INTERNAL);
    assert(buf);
    lv_disp_draw_buf_init(&draw_buf, buf, NULL, LVGL_BUF_SIZE);

    /* Register display driver */
    static lv_disp_drv_t disp_drv;
    lv_disp_drv_init(&disp_drv);
    disp_drv.hor_res = panel->getLCD()->getFrameWidth();
    disp_drv.ver_res = panel->getLCD()->getFrameHeight();
    disp_drv.flush_cb = lvgl_flush_cb;
    disp_drv.draw_buf = &draw_buf;
    lv_disp_drv_register(&disp_drv);

    /* Register touch input */
    if (panel->getTouch() != NULL) {
        static lv_indev_drv_t indev_drv;
        lv_indev_drv_init(&indev_drv);
        indev_drv.type = LV_INDEV_TYPE_POINTER;
        indev_drv.read_cb = lvgl_touch_cb;
        lv_indev_drv_register(&indev_drv);
    }

    /* Start LVGL task with recursive mutex */
    lvgl_mux = xSemaphoreCreateRecursiveMutex();
    xTaskCreate(lvgl_task, "lvgl", 4096, NULL, 2, NULL);

    /* ---- Create your UI here ---- */
    xSemaphoreTakeRecursive(lvgl_mux, portMAX_DELAY);
    // Example: lv_obj_t *label = lv_label_create(lv_scr_act());
    // lv_label_set_text(label, "Hello World!");
    // lv_obj_center(label);
    xSemaphoreGiveRecursive(lvgl_mux);
}

void loop()
{
    delay(5);
}
```

## Thread Safety

LVGL is **not thread-safe**. All LVGL API calls from outside the LVGL task must be wrapped:

```cpp
xSemaphoreTakeRecursive(lvgl_mux, portMAX_DELAY);
// ... LVGL API calls ...
xSemaphoreGiveRecursive(lvgl_mux);
```
