# Battery & Runtime Budget (Rev A)

---

# 1. Battery

* **Capacity (nominal):** 200 mAh
* **Usable fraction:** 80% (aging + cutoff margin)
* **Usable capacity:** 160 mAh

---

# 2. Assumptions

* Day length: 24 hours
* System rail: **3.0V_SYS**
* Watch is OFF while charging (no runtime counted during charge)

---

# 3. Current Consumption (Typical + Peak)

---

## MCU — nRF52832 (MDBT42Q)

* **Deep sleep (System ON, RTC wake):** 1.9 µA @ 3 V
* **Idle (CPU awake, light processing):** 3.0 mA (placeholder)
* **BLE advertising average:** 0.02 mA (long interval, short event; placeholder)
* **BLE connected average:** 3.0 mA (config activity; placeholder)
* **Peak TX burst:** 7.1 mA @ 0 dBm (1 Mb/s, HFXO)

---

## IMU — LSM6DSOX

* **Step / wake mode:** 26 µA (low-power, ODR = 50 Hz)
* **Active (high-performance):** 170 µA

---

## Ambient Light Sensor — VEML7700

* **Low-power PSM (sample mode):** 2 µA typical (PSM = 11, ~4100 ms refresh)
* **Typical duty cycle:** Always in low-power PSM

  * Brief I2C reads ignored
* **Shutdown current:** 0.5 µA (if explicitly shut down)

---

## Display — Sharp Memory LCD (LS013B7DH03)

* **Static (no updates):** 20 µA max
* **Dynamic update:** 340 µA max during active update
* **Average current:** Derived from wake/update behavior below

---

## LDO — TPS7A02-3.0

* **Quiescent current (IQ):** 25 nA typical (~0.000025 mA)
* Dropout handled via brownout/cutoff strategy

---

# 4. Daily Usage Scenario — Typical Day (Rev A)

* Wake events/day: 200
* Wake duration: 3 s per event
* BLE config usage: 5 minutes/day (300 s)
* Display updates: During wake only
* Steps/day: 8,000–12,000

---

# 5. Typical-Day Average Current Estimate

---

## Baseline (Always-On)

* MCU sleep: 1.9 µA
* IMU low-power: 26 µA
* ALS low-power: 2 µA
* Display static: 20 µA
* LDO IQ: 0.025 µA

**Baseline total ≈ 49.9 µA (0.0499 mA)**

---

## Wake-Time Extra (Over Baseline)

Per wake:

* MCU active vs sleep:

  * (3.0 mA − 0.0019 mA) ≈ +2.998 mA

* Display dynamic vs static:

  * (0.340 mA − 0.020 mA) = +0.320 mA

* ALS temporary increase (placeholder):

  * (0.045 mA − 0.002 mA) = +0.043 mA

**Extra during wake ≈ +3.361 mA**

Total wake time/day:
200 × 3 s = 600 s

---

## BLE Config Extra (Over Baseline)

* MCU + radio active: 3.0 mA for 300 s
* Display dynamic during config: +0.320 mA

**Extra during BLE ≈ +3.318 mA**

---

## Average Current Calculation

```
I_avg ≈ baseline
+ (3.361 mA)(600 / 86400)
+ (3.318 mA)(300 / 86400)
```

**Result:**

I_avg ≈ 0.085 mA

---

## Battery Life (Typical Day)

```
Life_hours = 160 mAh / 0.085 mA ≈ 1880 hours
```

≈ 78 days

Margin vs 24 hours: Extremely high (>7000%)

---

# Interpretation (Typical Day)

24-hour runtime is achievable with large margin **if firmware reliably reaches sleep states** and the display remains mostly static.

---

# 6. Worst Realistic Day (Stress Test)

* Wake events/day: 1000
* Wake duration: 5 s per event
* BLE config: 30 minutes/day (1800 s)
* ALS forced active: 45 µA constant
* MCU active during wakes/BLE: 5 mA (conservative placeholder)
* Display dynamic during wakes/BLE: 0.340 mA max

---

## Result (Conservative Assumptions)

I_avg ≈ 0.51 mA

```
Life_hours = 160 mAh / 0.51 mA ≈ 313 hours
```

≈ 13 days

Margin vs 24 hours ≈ 1200%

---

# 7. Open Items (Replace Placeholders)

* Replace MCU “idle” and “BLE connected average” with Nordic current-table values or Power Profiler measurements
* Confirm display update behavior (partial vs full refresh, EXTCOMIN/VCOM handling)
* Validate board-level sleep current on hardware (leakage, pull-ups, sensor defaults)

---

# 8. Goal Check

Requirement:

* ≥ 24 hours runtime
* ≥ 20% margin

**Status: PASS**

Even under conservative worst-day assumptions, runtime significantly exceeds 24 hours.
