# Axion: 6-Axis Robot Arm Controller

Open-source controller board for a 6-axis articulated robot arm, designed in KiCad. It is built around an STM32F103C8T6 and drives six servos, reads per-joint IMUs for orientation feedback, and supports USB, CAN, and NRF24L01 wireless control.

![Assembled board](https://user-images.githubusercontent.com/38166489/77441627-aaf57800-6e0f-11ea-874f-cbef119c32d2.jpg)

## Features

- **MCU:** STM32F103C8T6
- **Servo outputs:** 6, each with its own MOSFET driver stage
- **IMU inputs:** 6 I2C headers, one per joint, for orientation feedback
- **Connectivity:**
  - USB via CP2102N (USB-UART)
  - CAN bus via TCAN330
  - NRF24L01 socket for wireless control
- **Power:** 12 V input, stepped down to 5 V with a TPS5331x buck converter, then to 3.3 V with an LM3480 linear regulator
- **Programming and debug:** SWD header

## Gallery

| Board (view 1) | Board (view 2) |
|---|---|
| ![View 1](https://user-images.githubusercontent.com/38166489/77441627-aaf57800-6e0f-11ea-874f-cbef119c32d2.jpg) | ![View 2](https://user-images.githubusercontent.com/38166489/77441630-ac26a500-6e0f-11ea-909f-31d1b2f50830.jpg) |

| Front copper | Back copper |
|---|---|
| ![Front copper](https://user-images.githubusercontent.com/38166489/77441622-a92bb480-6e0f-11ea-828d-e1a354881061.PNG) | ![Back copper](https://user-images.githubusercontent.com/38166489/77441641-adf06880-6e0f-11ea-8a5c-0b5e2344863a.PNG) |

## Repository structure

```
axion/
├── hardware/          # KiCad project (schematic, PCB, libraries)
├── gerbers/           # Fabrication outputs ready to send to a PCB house
├── docs/              # Schematic PDF, pinout, design notes
├── firmware/          # STM32 firmware (work in progress)
├── bom/               # Bill of materials
├── LICENSE
└── README.md
```

## Pin summary

> Fill in from your schematic.

| Function | MCU pin | Notes |
|---|---|---|
| Servo 1 to 6 | _TBD_ | PWM via MOSFET stage |
| I2C (IMUs) | _TBD_ | See IMU addressing below |
| USB-UART | _TBD_ | CP2102N |
| CAN TX / RX | _TBD_ | TCAN330 |
| NRF24L01 (SPI + CE/CSN/IRQ) | _TBD_ | |
| SWD | SWDIO / SWCLK | Programming header |

## Power

| Rail | Source | Feeds |
|---|---|---|
| 12 V | External supply | Input to buck converter |
| 5 V | TPS5331x buck | Servos, 5 V logic |
| 3.3 V | LM3480 from 5 V | MCU, NRF24L01, CAN transceiver, IMUs |

Notes:

- Size the 12 V supply for the **worst-case combined servo current**, including stall, not just the average.
- The 3.3 V rail comes from a small linear regulator. Check the total 3.3 V load (MCU + NRF24L01 + CAN transceiver + IMUs) against its rating.
- Keep servo return currents separate from the MCU and IMU ground area where possible.

## IMU addressing

Six IMUs share the I2C bus. Many IMUs offer only two selectable addresses, so describe here how you handle this, for example:

- an I2C multiplexer, or
- separate buses, or
- address pin strapping per joint

_TBD: document the approach used on this board._

## Getting started

### 1. Fabricate the board

1. Take the files in `gerbers/` and upload them to your PCB manufacturer.
2. Order components from the BOM in `bom/`.
3. Assemble the board and inspect the power section before applying 12 V.

### 2. Bring-up checklist

1. Power the board from a current-limited 12 V supply.
2. Verify the 5 V and 3.3 V rails with a multimeter.
3. Connect an SWD programmer (ST-Link or similar) and confirm the MCU is detected.
4. Flash a blink test, then test the servo outputs one at a time with no load.
5. Connect IMUs and scan the I2C bus.

### 3. Firmware

_Firmware is a work in progress._ Planned build and flash instructions:

```bash
cd firmware
# build and flash instructions go here
```

## Roadmap

- [ ] Board bring-up and validation
- [ ] Basic firmware: servo control, IMU reads
- [ ] CAN and wireless command interface
- [ ] Inverse kinematics for the 6-axis arm
- [ ] Mechanical design / arm assembly

## Contributing

Issues and pull requests are welcome, especially feedback on power delivery for multi-servo setups, layout, and firmware.

## License

Choose a license for the hardware and firmware and add a `LICENSE` file. Common choices are CERN-OHL-P or CERN-OHL-S for hardware and MIT or Apache-2.0 for firmware.
