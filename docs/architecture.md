# Film Watch Architecture — Rev A

---

# 1. Power Tree

```
LiPo Battery (2.7 V – 4.2 V)
    → Charger IC
    → Protection
    → 3.0 V LDO
    → System Rail (MCU, IMU, ALS, Display)
```

* All digital and sensor components operate from a single regulated **3.0V_SYS** domain.
* Simplified single-rail architecture for Rev A.

---

# 2. Power Domains (Rev A)

## VBAT

* 2.7 V – 4.2 V
* Direct from 1S LiPo

## 3.0V_SYS

* Regulated by **TPS7A02-3.0**
* Powers:

  * MCU
  * IMU
  * ALS
  * Display

All digital logic and sensors operate exclusively from 3.0V_SYS.

---

# 3. Bus Topology

---

# I2C Bus (Shared — I2C0)

## Devices

* LSM6DSOX (IMU)

  * Address: 0x6A / 0x6B (selectable)
* VEML7700 (ALS)

## Pull-Ups

* SDA → 3.0V_SYS via 4.7 kΩ
* SCL → 3.0V_SYS via 4.7 kΩ

## Rules (Rev A)

* Keep traces short
* No additional devices on bus in Rev A
* Firmware implements bus recovery:

  * Toggle SCL if SDA becomes stuck

---

# SPI Bus (SPI0 — Dedicated)

## Device

* Sharp Memory LCD

## Signals

* SCK
* MOSI
* CS
* DISP_EN (if used)
* EXTCOMIN (if required by LCD variant)

## Rules

* Dedicated bus (no sharing)
* Active only during wake window

---

# 4. Interrupt Architecture (Rev A Locked)

---

## Wake Sources

* IMU INT1 → MCU wake-capable GPIO
* 3 Button GPIO interrupts
* RTC periodic tick (timekeeping)

---

## IMU Interrupt Usage

* Only **INT1** used in Rev A
* INT1 handles:

  * Raise-to-wake
  * Step detection

Firmware determines event source via IMU status registers.

---

# 5. Buttons

* 3 side buttons
* Connected to MCU GPIO
* Use internal pull-ups
* Interrupt-capable inputs

---

# 6. USB-C Power Input (If Used)

* Configured as sink (UFP)
* 5.1 kΩ pulldown resistors:

  * CC1 → GND
  * CC2 → GND

Power-only implementation for Rev A.

---

# 7. Power Enable Behavior (Rev A Locked)

## Charging State

* Watch is OFF while USB is present
* No firmware operation during charging
* No BLE
* No display updates

## Normal Operation

* System rail: 3.0V_SYS from TPS7A02-3.0
* Default runtime state: Deep Sleep

  * IMU running in low-power mode
  * MCU in sleep
  * Display static

---

# Rev A Design Principles

* Single regulated 3.0 V domain
* Minimal bus complexity
* Dedicated SPI for display
* Shared I2C limited to two sensors
* Hardware kept simple; firmware handles recovery and wake logic
