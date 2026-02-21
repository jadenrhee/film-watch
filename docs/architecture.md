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
