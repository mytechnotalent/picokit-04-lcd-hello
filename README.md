![picokit-04-lcd-hello](https://raw.githubusercontent.com/mytechnotalent/picokit-04-lcd-hello/main/picokit-04-lcd-hello.png)

<br>

## FREE Reverse Engineering Self-Study Course [HERE](https://github.com/mytechnotalent/reverse-engineering)
## FREE Embedded Hacking Course [HERE](https://github.com/mytechnotalent/Embedded-Hacking)

<br>

# PICOKIT-04 LCD HELLO

### 1602 I2C LCD Text and Authenticated Heartbeat
#### Lesson 4 of the Picokit Series

<br>

***
**LEGAL DISCLAIMER:**
The information, tools, and code provided in this repository and course are strictly for educational, research, and defensive purposes only.

You are explicitly prohibited from using any materials contained herein to access, test, modify, or exploit any device, network, or system that you do not own 100% or for which you do not have explicit, documented, and legally binding authorization to interact with.

By using this repository and course, you acknowledge and agree that:

1. Any illegal, unauthorized, or malicious use of this information is solely your responsibility.
2. The author(s) and contributor(s) of this repository and course shall not be held liable for any damages, legal repercussions, criminal charges, or unauthorized actions resulting from the use, misuse, or abuse of the contents herein.
3. You will comply with all applicable local, state, national, and international laws regarding cybersecurity and computer fraud.

**IF YOU DO NOT AGREE WITH THESE TERMS, DO NOT USE THIS REPOSITORY AND COURSE.**
***

<br>
<br>

## Overview

The fourth Picokit lesson. The node drives a 1602 LCD over the
PCF8574 I2C backpack and prints two lines of text, and every five
seconds it transmits an authenticated heartbeat over LoRa to a
Python gateway that logs and displays it. It reuses the standard
node shape from the earlier lessons and adds an HD44780 display
driver on I2C1.

<br>

## What it teaches

- Driving an HD44780 1602 LCD through a PCF8574 I2C backpack.
- The 4-bit wake-up and command sequence for the display.
- Sealing a tiny JSON body with Argon2id and XChaCha20-Poly1305 and
  sending it with an AT+SEND over the RYLR998.
- The gateway side: receive, authenticate, reject, log, and display.

<br>

## Hardware

| Peripheral | Pico 2 pin | Role |
| --- | --- | --- |
| 1602 LCD SDA | GP2 | I2C data |
| 1602 LCD SCL | GP3 | I2C clock |
| Onboard LED | GP25 | heartbeat, one blink per refresh |
| RYLR998 | GP8 TX / GP9 RX | LoRa heartbeat |
| Debug Probe | SWCLK/SWDIO/GND, GP0/GP1 | SWD and the console |

<br>

## How it works

The node runs `monitor_step` in a loop. Every second it pushes
`PICOKIT-04 LCD` and `HELLO WORLD` to the 1602 at address 0x27,
blinking GP25 once per refresh, and every 5 seconds it seals
`{"n":4,"s":<seq>,"c":<counter>}` with the field key and
sends it over LoRa. The gateway authenticates each frame and only
then parses it.

<br>

## Build and flash

```bash
cd firmware
cmake -S . -B build -G Ninja -DPICO_BOARD=pico2 -DPICO_PLATFORM=rp2350-arm-s
cmake --build build
openocd -f interface/cmsis-dap.cfg -f target/rp2350.cfg \
  -c "program build/picokit_04_lcd_hello.elf verify reset exit"
```

<br>

## Watch the node

Open the console at 115200 and reset:

```text
BOOT
I2C scan:
  found 0x27
=== PICOKIT-04 LCD HELLO // 1602 TEXT + AUTHENTICATED HEARTBEAT ===
LCD step=0 seq=0
RX from 0x0001, N bytes
```

<br>

## The gateway

```bash
cd gateway
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
python3 listen.py --port /dev/cu.usbserial-A50285BI --hub 0001 --network 18 --db gateway.db
```

It prints `OK node=4 rssi=...` per authenticated heartbeat.
The terminal dashboard `python3 tui.py --db gateway.db` and the web dashboard
`python3 web/app.py --db gateway.db` show the same rows.

<br>

## Verify

```bash
python3 .opencode/skill/embedded-c-standard/audit_c_standard.py
python3 .opencode/skill/embedded-python-standard/audit_python_standard.py
python3 .opencode/skill/iot-readme-standard/validate_readme.py
python3 .opencode/skill/iot-banner-standard/validate_banner.py
python3 scripts/run_tests.py
python3 scripts/check_coverage.py
```

<br>

# Next
[picokit-05-lcd-live](https://github.com/mytechnotalent/picokit-05-lcd-live)

<br>

# License
[MIT License](https://github.com/mytechnotalent/picokit-04-lcd-hello/blob/main/LICENSE)
