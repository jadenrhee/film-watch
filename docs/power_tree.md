Power Tree (Rev A)

Sources

USB_5V: from USB connector

VBAT: 1S LiPo, 2.7V–4.2V

Charging Path

USB_5V → MCP73831 → VBAT

Charge current target: 100 mA (for ~200 mAh pack)

Main Regulation

VBAT → TPS7A02-3.0 → 3.0V_SYS

Rails

3.0V_SYS powers:

nRF52832 module (MDBT42Q)

IMU (LSM6DSOX)

ALS (VEML7700)

Memory LCD

Power States

S0 (Active): MCU on, display updates allowed, sensors active

S1 (Idle): MCU mostly sleeping, IMU running, periodic ALS sampling

S2 (Deep sleep): MCU sleep, IMU in low-power step/interrupt mode, ALS off or lowest duty

Charging state: USB_5V present, battery charging, system may be on/off (define)

Cutoffs

Battery cutoff target: VBAT ~3.1 V under load (to avoid brownouts during BLE bursts)

Brownout strategy: define BOR threshold + behavior

Charging behavior (Rev A)

When USB_5V is present, the watch is OFF (no normal operation while charging).

Charging path: USB_5V → LiPo charger → VBAT

System rail 3.0V_SYS is disabled during charging (firmware does not run; no BLE; no display updates).

Rationale

Avoids power-path complexity in Rev A.

Simplifies bring-up and reduces brownout/edge-case risks during charge.

Sleep plan (Rev A)

Default state is Deep Sleep (S2). CPU off, RTC running.

Wake sources:

IMU interrupt (raise-to-wake / step events)

Button GPIO interrupt

RTC periodic tick (timekeeping / housekeeping)

I2C bus is only enabled during wake window; disabled otherwise.

Display SPI only active during wake window; display static otherwise.

BLE disabled by default; enabled only during user-initiated config window.

Sleep risks

Any floating GPIO, pull-up leakage, or sensor default mode can prevent achieving the µA-level sleep current.

Board-level sleep current must be measured during bring-up and compared to budget.
