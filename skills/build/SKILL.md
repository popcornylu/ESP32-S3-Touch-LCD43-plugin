---
name: build
description: Build, upload, monitor, or clean the PlatformIO ESP32 project
user-invoked: true
allowed-tools:
  - Bash
  - Read
---

# /build — PlatformIO Build Commands

Run PlatformIO commands for the ESP32 project. Activate the Python venv first, then execute the appropriate command.

## Usage

- `/build` — Compile the project
- `/build upload` — Compile and upload to the board
- `/build monitor` — Open serial monitor
- `/build clean` — Clean build artifacts

## Instructions

1. Always activate the venv before running PlatformIO:
   ```bash
   source .venv/bin/activate
   ```
   If `.venv` does not exist, create it first:
   ```bash
   uv venv && source .venv/bin/activate && uv pip install platformio
   ```

2. Parse the user's argument from the `/build` invocation. The text after `/build` is the subcommand. Based on the subcommand (or lack thereof), run:
   - **(no argument)**: `pio run`
   - **upload**: `pio run --target upload`
   - **monitor**: `pio device monitor`
   - **clean**: `pio run --target clean`

3. If the build fails, read the error output and help the user fix the issue.
