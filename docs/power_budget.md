Battery:

Capacity: 200 mAh (nominal)

Usable fraction: 80% (aging + cutoff margin)

Usable mAh: 160 mAh

Assumptions:

Day length: 24 hours

System rail: 3.0V_SYS

Watch is OFF while charging (no runtime counted during charge)

Currents (typical + peak):

MCU (nRF52832 / MDBT42Q module)

Deep sleep (System ON, RTC wake): 1.9 µA (typical @ 3 V)

Idle (CPU awake, light processing): 3.0 mA (assumption: short bursts; conservative placeholder)

BLE advertising average: 0.02 mA (assumption: long interval, short event; placeholder)

BLE connected average: 3.0 mA during config activity (assumption: active connection + some traffic; placeholder)

Peak TX burst: 7.1 mA @ 0 dBm (TX @ 1 Mb/s, HFXO)

IMU (LSM6DSOX)

Step / wake mode: 26 µA (accelerometer low-power mode, ODR=50 Hz)

Active (accelerometer high-performance): 170 µA

ALS (VEML7700)

Sample mode current (low-power PSM): 2 µA typical (PSM=11, refresh ~4100 ms)

Duty cycle (typical): always in low-power PSM (2 µA avg), plus brief I2C reads (ignored)

Shutdown current: 0.5 µA typ (only if we explicitly shut it down)

Display (Sharp Memory LCD LS013B7DH03)

Static (no updates): 20 µA max

Dynamic update: 340 µA max while actively updating

Estimated average current (typical): computed from wake/update behavior below

LDO overhead (TPS7A02-3.0)

IQ: 25 nA typical (~0.000025 mA)

Worst-case dropout event: handled in separate brownout/cutoff strategy

Daily usage scenario — Typical Day (Rev A)

Wake events/day: 200

Wake duration: 3 s per event (screen on + quick UI)

BLE config minutes/day: 5 minutes (connected and active)

Display updates/day: during wake only

Steps/day: 8000–12000 (IMU stays in low-power step/wake mode)

Typical-day average current estimate:

Baseline (always-on):

MCU sleep: 1.9 µA

IMU low-power: 26 µA

ALS low-power: 2 µA

Display static: 20 µA

LDO IQ: ~0.025 µA
Baseline total ≈ 49.9 µA = 0.0499 mA

Wake-time extra (over baseline), per wake:

MCU active instead of sleep: + (3.0 mA - 0.0019 mA) ≈ +2.998 mA

Display dynamic instead of static: + (0.340 mA - 0.020 mA) = +0.320 mA

ALS temporarily active (conservative): + (0.045 mA - 0.002 mA) = +0.043 mA (placeholder for “more frequent reads”)
Extra during wake ≈ +3.361 mA

Total wake time/day = 200 * 3 s = 600 s

BLE config extra (over baseline):

Assume MCU+radio active: 3.0 mA for 5 min/day (300 s)

Assume display dynamic during BLE config: +0.320 mA (conservative)
Extra during BLE ≈ +3.318 mA

Compute average current:
I_avg ≈ baseline
+ (3.361 mA)(600/86400)
+ (3.318 mA)(300/86400)

Result:

I_avg ≈ 0.085 mA

Life_hours = 160 mAh / 0.085 mA ≈ 1880 hours (~78 days)

Margin vs 24 hours ≈ extremely high (>7000%)

Interpretation:

24 hours is achievable with large margin if firmware actually reaches sleep states and display is mostly static.

Worst realistic day scenario (stress test)

Wake events/day: 1000

Wake duration: 5 s per event (heavy use)

BLE config minutes/day: 30 minutes (1800 s)

ALS: forced “more active” behavior: 45 µA constant (worst-case placeholder using non-PSM style activity level from app-note table)

MCU active during wakes/BLE: 5 mA placeholder (conservative)

Display dynamic during wakes/BLE: 0.340 mA max

Result (conservative placeholders):

I_avg ≈ 0.51 mA

Life_hours = 160 mAh / 0.51 mA ≈ 313 hours (~13 days)

Margin vs 24 hours ≈ ~1200%

Open items (must replace placeholders with measured or Nordic PS values):

Replace MCU “idle” and “BLE connected average” placeholders with values from Nordic current tables / Power Profiler measurements.

Confirm display update behavior in firmware (partial updates vs full refresh, EXTCOMIN/VCOM requirements).

Validate actual system sleep current on hardware (board-level leakage, pull-ups, sensor defaults).

Goal check:

≥24 hours with ≥20% margin: PASS (even under conservative “worst realistic day” placeholders)
