# GPIO Pinout — Waveshare ESP32-S3-Touch-LCD-4.3B

## LCD RGB Data Pins

| Signal | GPIO |
|---|---|
| D0 | GPIO14 |
| D1 | GPIO38 |
| D2 | GPIO18 |
| D3 | GPIO17 |
| D4 | GPIO10 |
| D5 | GPIO39 |
| D6 | GPIO0 |
| D7 | GPIO45 |
| D8 | GPIO48 |
| D9 | GPIO47 |
| D10 | GPIO21 |
| D11 | GPIO1 |
| D12 | GPIO2 |
| D13 | GPIO42 |
| D14 | GPIO41 |
| D15 | GPIO40 |

## LCD Control Pins

| Signal | GPIO |
|---|---|
| HSYNC | GPIO46 |
| VSYNC | GPIO3 |
| DE (Data Enable) | GPIO5 |
| PCLK (Pixel Clock) | GPIO7 |

## Touch I2C Pins

| Signal | GPIO |
|---|---|
| SDA | GPIO8 |
| SCL | GPIO9 |
| INT | GPIO4 |

## IO Expander I2C Pins

| Signal | GPIO |
|---|---|
| SDA | GPIO8 (shared with touch) |
| SCL | GPIO9 (shared with touch) |

## USB

| Signal | GPIO |
|---|---|
| USB D- | GPIO19 |
| USB D+ | GPIO20 |

## GPIO Occupation Summary

### Occupied GPIOs (do NOT use for user projects)

| GPIO | Used By |
|---|---|
| 0 | LCD D6 |
| 1 | LCD D11 |
| 2 | LCD D12 |
| 3 | LCD VSYNC |
| 4 | Touch INT |
| 5 | LCD DE |
| 7 | LCD PCLK |
| 8 | I2C SDA (touch + IO expander) |
| 9 | I2C SCL (touch + IO expander) |
| 10 | LCD D4 |
| 14 | LCD D0 |
| 17 | LCD D3 |
| 18 | LCD D2 |
| 19 | USB D- |
| 20 | USB D+ |
| 21 | LCD D10 |
| 38 | LCD D1 |
| 39 | LCD D5 |
| 40 | LCD D15 |
| 41 | LCD D14 |
| 42 | LCD D13 |
| 45 | LCD D7 |
| 46 | LCD HSYNC |
| 47 | LCD D9 |
| 48 | LCD D8 |

### Available GPIOs for User Projects

| GPIO | Notes |
|---|---|
| 6 | General purpose |
| 11 | General purpose |
| 12 | General purpose |
| 13 | General purpose |
| 15 | General purpose |
| 16 | General purpose |
| 43 | Default UART TX |
| 44 | Default UART RX |

> **Note**: GPIO43/44 are used for UART0 by default. If USB CDC is your primary serial interface, these can be repurposed. GPIO6, 11, 12, 13, 15, 16 are freely available for SPI, UART, ADC, or general I/O.
