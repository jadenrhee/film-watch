# System Architecture — Rev A (Locked Decisions)

---

# 1. System Rail Decision

* **Battery:** 1S LiPo (2.7 V – 4.2 V)
* **System rail:** 3.0V_SYS (regulated)

### Rationale

* Fully charged LiPo reaches 4.2 V
* Primary ICs are **not 4.2 V tolerant**
* nRF52832 module supply range: 1.7 V – 3.6 V

Therefore, VBAT must be regulated down to 3.0 V.

---

# 2. BLE MCU Module (Locked)

* **Part:** Raytac MDBT42Q (nRF52832 module)
* **Supply range:** 1.7 V – 3.6 V
* **System compatibility:** Fully compatible with 3.0V_SYS

### Notes

* Module used to reduce RF design risk on Rev A

---

# 3. IMU (Locked)

* **Part:** LSM6DSOX (STMicroelectronics)
* **Supply range:** 1.71 V – 3.6 V
* **IO supply:** Supported
* **System compatibility:** Fully compatible with 3.0V_SYS
* **Interface:** I2C
* **Address:** 0x6A / 0x6B (selectable via SDO)

### Use Case

* Step counting
* Raise-to-wake via interrupt

---

# 4. Ambient Light Sensor (Locked)

* **Part:** VEML7700 (Vishay)
* **Supply range:** 2.5 V – 3.6 V
* **System compatibility:** Fully compatible with 3.0V_SYS
* **Interface:** I2C

### Use Case

* Lux measurement
* EV / exposure recommendations

---

# 5. Display (Locked — Memory LCD)

* **Part:** Sharp LS013B7DH03
* **Size / Resolution:** 1.28", 128 × 128
* **Supply range:** 2.7 V – 3.3 V
* **System compatibility:** Fully compatible with 3.0V_SYS
* **Interface:** SPI

### Rationale

* Sunlight readable
* Extremely low static power

### Mechanical Note

* Confirm connector / FFC strategy with enclosure

---

# 6. Main System Regulator (Locked)

* **Part:** TPS7A02-3.0 (Texas Instruments)
* **Function:** LiPo → 3.0V_SYS LDO

### Stability Requirements

* Minimum effective Cout: ≥0.5 µF
* TI recommendation: ≥1 µF ceramic for improved transient response

### Capacitors (Rev A Selection)

To maintain effective capacitance under DC bias:

* **Cin:** 2.2 µF X7R ceramic (≥6.3 V rating)
* **Cout:** 2.2 µF X7R ceramic (≥6.3 V rating)

### Enable Pin

* EN floating behaves as disabled per datasheet
* Treat EN as a controlled signal

---

# 7. LiPo Charger (Locked — Rev A Simplicity)

* **Part family:** MCP73831 (Microchip)
* **Function:** Single-cell Li-Ion / LiPo linear charger
* **Regulation voltage:** Select 4.2 V termination variant

### Recommended Variant

* MCP73831-2 (4.2 V regulation option)
* Confirm exact suffix per distributor availability

### Charge Current (Rev A)

* Target: 100 mA
* Equivalent to 0.5C for 200 mAh battery

### Notes

* Linear charger
* Manage thermals via copper pour near IC

---

# 8. USB Power Input

## Option A (Recommended — Rev A)

* **Connector:** USB-C receptacle (power-only)
* **Requirement:**

  * 5.1 kΩ pull-down on CC1 to GND
  * 5.1 kΩ pull-down on CC2 to GND
* Advertises as sink (UFP)

### Mechanical Note

* Choose receptacle based on enclosure cutout and footprint library

---

## Option B (Simpler Footprint)

* Micro-USB receptacle (power-only)

---

# 9. Battery Connector (Mechanical Dependent)

## Option A

* JST-PH 2-pin (common LiPo connector)

## Option B

* JST-SH 2-pin (smaller, less user-friendly)

Select after final battery choice.

---

# 10. Buttons (3 Side Buttons)

* **Type:** Side-actuated tactile switch (SMD)
* **Electrical:**

  * Connect to MCU GPIO
  * Use pull-ups
  * Optional RC + ESD protection

### Mechanical Note

Switch selection depends on enclosure wall thickness and actuator geometry.

---

# 11. Required Passives (Rev A Defaults)

---

## I2C Pull-Ups (to 3.0V_SYS)

* SDA: 4.7 kΩ
* SCL: 4.7 kΩ

Adjust if increasing I2C speed or using long traces.

---

## USB-C CC Resistors

* CC1 → GND: 5.1 kΩ
* CC2 → GND: 5.1 kΩ

---

## Decoupling

Place near each IC or supply pin cluster:

* 0.1 µF X7R at each VDD group
* 1.0 µF bulk per rail region (near module + near sensors)

---

## Optional Bulk Cap (Load Step Stability)

* 4.7 µF X7R on 3.0V_SYS

  * Place near display connector / module area

---

## Charger Capacitors (Typical)

* 1.0 µF at VDD (USB input) close to MCP73831 VDD
* 1.0 µF at VBAT close to battery pin

  * Confirm exact values in MCP datasheet

---

# 12. Programming and Debug

## SWD Pads

* SWDIO
* SWCLK
* GND
* 3.0V_SYS
* RESET (if available)

## Recommended Test Pads

* VBAT
* 3.0V_SYS
* GND
* SDA
* SCL
* SPI lines (minimum)

---

# 13. Compatibility Summary (Week-1 Pass Conditions)

All ICs operate safely within 3.0V_SYS:

* MDBT42Q (nRF52832): 1.7 V – 3.6 V
* LSM6DSOX: 1.71 V – 3.6 V
* VEML7700: 2.5 V – 3.6 V
* LS013B7DH03 Memory LCD: 2.7 V – 3.3 V

Regulator stability requirement captured:

* TPS7A02 output capacitance meets datasheet minimums.
