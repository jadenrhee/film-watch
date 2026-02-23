System rail decision

Battery: 1S LiPo (2.7V–4.2V)
System rail: 3.0V_SYS regulated
Reason: battery reaches 4.2V; main ICs are not 4.2V tolerant (nRF52832 module supply 1.7–3.6V).

BLE MCU module (Locked)

Part: Raytac MDBT42Q (nRF52832 module)
Supply: 1.7V–3.6V (compatible with 3.0V_SYS)
Notes: BLE module chosen to reduce RF risk on Rev A.

IMU (Locked)

Part: LSM6DSOX (STMicroelectronics)
Supply: 1.71V–3.6V analog supply; IO supply supported (compatible with 3.0V_SYS)
Interface: I2C planned
I2C address: 0x6A/0x6B (selectable via SDO)
Use: step counting + raise-to-wake via interrupts.

Ambient light sensor (Locked)

Part: VEML7700 (Vishay)
Supply: 2.5V–3.6V (compatible with 3.0V_SYS)
Interface: I2C
Use: lux measurement for EV/exposure recommendations.

Display (Locked decision: Memory LCD)

Part: Sharp Memory LCD LS013B7DH03 (1.28", 128x128)
Supply: 2.7V–3.3V (compatible with 3.0V_SYS)
Interface: SPI (serial)
Reason: sunlight readable + low static power.

MECH NOTE: confirm connector/FFC strategy with enclosure.

Main system regulator (Locked)

Part: TPS7A02-3.0 (Texas Instruments)
Function: LiPo -> 3.0V_SYS LDO
Stability: requires ≥0.5µF effective Cout; TI recommends ≥1µF ceramic for transient response

Caps (Rev A choice to ensure effective capacitance under bias):
Cin: 2.2µF X7R ceramic (≥6.3V rating)
Cout: 2.2µF X7R ceramic (≥6.3V rating)

Enable: EN floating behaves as disabled per datasheet wording (treat EN as controlled signal).

LiPo charger (Locked for Rev A simplicity)

Part family: MCP73831 (Microchip)
Function: single-cell Li-Ion/Li-Po linear charger, programmable charge current with PROG resistor
Regulation voltage options include 4.2V variants (select 4.2V termination)

Recommended specific orderable: MCP73831-2 (4.2V regulation option; exact suffix per distributor availability)

Charge current target (Rev A): 100mA (0.5C for 200mAh pack)
Notes: linear charger; manage thermals via copper pour near IC.

USB power input connector (Rev A)

Option A (recommended for Rev A): USB-C receptacle (power-only)
Requirement: two 5.1k pull-downs from CC1 and CC2 to GND to advertise as a sink (UFP).

Parts (MECH-DEPENDENT): choose a USB-C receptacle that matches your enclosure cutout and footprint library.

Option B (simpler footprint): Micro-USB receptacle (power-only)

Battery connector (MECH-DEPENDENT)

Option A: JST-PH 2-pin (common LiPo connector)
Option B: JST-SH 2-pin (smaller, harder to plug/unplug)

Pick after you choose the actual battery.

Buttons (3 side buttons)

Part type: side-actuated tactile switch (SMD)
Electrical: connect to MCU GPIO with pull-ups; optional RC + ESD strategy per layout

MECH-DEPENDENT: exact switch model depends on enclosure wall thickness and actuator geometry.

Required passives (Rev A default values)

I2C pull-ups (to 3.0V_SYS):

SDA pull-up: 4.7k

SCL pull-up: 4.7k
(Adjust later if you run faster I2C or have long traces.)

USB-C CC resistors (if USB-C):

CC1 to GND: 5.1k

CC2 to GND: 5.1k

Decoupling (place at each IC/module supply pin cluster):

0.1µF X7R at each VDD pin group

1.0µF bulk per rail region (near module + near sensors)

Optional bulk near load steps:

4.7µF X7R on 3.0V_SYS near display connector / module area

Charger caps (typical):

1.0µF at VDD (USB input) close to MCP73831 VDD pin

1.0µF at VBAT close to battery pin (confirm exact in MCP datasheet)

Programming / debug

SWD pads: SWDIO, SWCLK, GND, 3.0V_SYS (and RESET if available)
Test pads: VBAT, 3.0V_SYS, GND, SDA, SCL, SPI lines (at minimum)

Compatibility summary (Week-1 pass conditions)

All ICs operate within 3.0V_SYS:

MDBT42Q (nRF52832): 1.7–3.6V

LSM6DSOX: 1.71–3.6V

VEML7700: 2.5–3.6V

LS013B7DH03 Memory LCD: 2.7–3.3V

Regulator stability requirement captured for TPS7A02 output capacitance.
