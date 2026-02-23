***Film watch architecture***

***Power Tree*** 

LiPo Battery (2.7V–4.2V)
→ Charger IC
→ Protection
→ 3.0V LDO
→ System Rail (MCU, IMU, ALS, Display)

All digital and sensor components operate from a single regulated 3.0V domain.

***Bus Topology***

I2C Bus:

→LSM6DSOX

→VEML7700
  Shared bus with pull-ups to 3.0V.

SPI Bus:

→Memory LCD

Interrupt Lines:

→IMU INT1 → MCU wake pin

→Optional ALS interrupt

Buttons:

→3 GPIO inputs with internal pull-ups.

Power domains (Rev A)

VBAT: 2.7V–4.2V from 1S LiPo

3.0V_SYS: regulated by TPS7A02-3.0; all digital + sensors run from 3.0V_SYS

I2C topology

3.0V pull-ups on SDA/SCL

Devices: LSM6DSOX (0x6A/0x6B selectable) and VEML7700 (I2C)

Bus recovery plan: firmware toggles SCL if SDA stuck (to be implemented in firmware layer)

USB-C power input (if used)

Sink configuration uses 5.1k pulldowns on CC1/CC2 to GND
