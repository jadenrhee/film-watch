***parts selection***

***System Voltage Decision***

System rail: 3.0V regulated
Battery chemistry: 1S LiPo (2.7V–4.2V)
Regulation strategy: Ultra-low IQ LDO
Battery cutoff target: 3.0V

why?:
Using a 3.0V regulated rail simplifies power architecture and allows the use of a low quiescent current LDO rather than a buck-boost converter. This reduces design complexity, switching noise, and risk during Rev A bring-up.

***MCU***

Part: Raytac MDBT42Q (nRF52832 module)
Manufacturer: Raytac / Nordic Semiconductor
Core SoC: nRF52832
Operating Voltage: 1.7V–3.6V
Interface: GPIO, I2C, SPI, BLE
Why chosen: Pre-certified BLE module reduces RF design risk for Rev A.

Electrical Compatibility:
Fully compatible with 3.0V rail.

***IMU***

Part: LSM6DSOX
Manufacturer: STMicroelectronics
Operating Voltage: 1.71V–3.6V
Interface: I2C (planned)
Interrupts: INT1, INT2 used for step + wake
Why chosen: Integrated step detection + low power modes.

Electrical Compatibility:
Fully compatible with 3.0V rail.

***Ambient Light Sensor*** 
for the light meter

Part: VEML7700
Manufacturer: Vishay
Operating Voltage: 2.5V–3.6V
Interface: I2C
Why chosen: Lux measurement suitable for exposure meter conversion.

Electrical Compatibility:
Fully compatible with 3.0V rail.

***Display***

Part: (insert exact Memory LCD model here)
Operating Voltage: Typically 2.7V–3.3V
Interface: SPI
Why chosen: Sunlight readability + ultra-low static power.

Electrical Compatibility:
Must confirm exact model supports 3.0V operation.

***LDO*** 

Requirement:
Input: Up to 4.2V
Output: 3.0V
Quiescent Current: <5 µA target
Dropout Voltage: <200mV at expected load

Candidate: (to be selected)

Selection Criteria:
Lowest quiescent current while maintaining stable regulation under BLE TX bursts.

***Battery***

Type: 1S LiPo
Capacity: ~200 mAh
Nominal Voltage: 3.7V
Max Voltage: 4.2V

looking for a 1-day runtime with moderate BLE + sensor usage.


***Charger IC*** (Candidate)

Requirement:
Single-cell LiPo charger
USB input
Charge current configurable
Thermal regulation

Candidate: (to be selected)

