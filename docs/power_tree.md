# Power Tree (Rev A)

---

## 1. Sources

* **USB_5V** — From USB connector
* **VBAT** — 1S LiPo (2.7 V – 4.2 V)

---

## 2. Charging Path

```
USB_5V → MCP73831 → VBAT
```

* **Charge current target:** 100 mA
* Intended for ~200 mAh LiPo pack

---

## 3. Main Regulation

```
VBAT → TPS7A02-3.0 → 3.0V_SYS
```

* Low-IQ LDO
* Regulated 3.0 V system rail

---

## 4. System Rail Distribution

### 3.0V_SYS powers:

* **nRF52832 module** (MDBT42Q)
* **IMU** (LSM6DSOX)
* **Ambient Light Sensor** (VEML7700)
* **Memory LCD**

---

# Power States

## S0 — Active

* MCU ON
* Display updates allowed
* Sensors active

---

## S1 — Idle

* MCU mostly sleeping
* IMU running
* Periodic ALS sampling

---

## S2 — Deep Sleep (Default)

* MCU in sleep mode
* IMU in low-power step / interrupt mode
* ALS OFF or lowest duty cycle
* RTC running

---

## Charging State

When **USB_5V** is present:

* Battery charging
* System may be ON or OFF (final behavior TBD)

---

# Cutoffs and Protection

## Battery Cutoff

* Target: **VBAT ≈ 3.1 V under load**
* Prevents brownouts during BLE bursts

---

## Brownout Strategy

* Define BOR threshold
* Define firmware recovery behavior

---

# Charging Behavior (Rev A)

When **USB_5V** is present:

* Watch is OFF
* No normal operation during charging
* No BLE
* No display updates
* Firmware not running
* 3.0V_SYS disabled

### Rationale

* Avoids power-path complexity in Rev A
* Simplifies bring-up
* Reduces brownout and edge-case risks

---

# Sleep Plan (Rev A)

Default state: **Deep Sleep (S2)**

* CPU OFF
* RTC running

---

## Wake Sources

* IMU interrupt (raise-to-wake / step events)
* Button GPIO interrupt
* RTC periodic tick (timekeeping / housekeeping)

---

## Peripheral Policy

* I2C bus enabled only during wake window
* Display SPI active only during wake window
* Display remains static otherwise
* BLE disabled by default

  * Enabled only during user-initiated configuration window

---

# Sleep Risks

The following can prevent achieving µA-level sleep current:

* Floating GPIO
* Pull-up leakage
* Sensors left in default modes

---

## Bring-Up Requirement

Board-level sleep current **must be measured during bring-up** and compared to the power budget.
