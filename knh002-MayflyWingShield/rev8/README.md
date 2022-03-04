# Mayfly Modbus wingboard PROPOSED RFC  Rev8

This is a proposed Request For Comment on a knh002-MafylWingShield Rev8  functionality.

The Mayfly rev 1.1 has an efficient nominal +12V generation, however it can't be switched off completely. 
The Mayfly 1.1 12v boost U10 MCP1665T-E powersupply referenced to 3.8V LiIon source, at room temperature 15C, was measured as able to supply 620mA for some seconds.
The released Mayfly 1.1 12V boost is likely to derive its power from a lower power switched supply so can be turned OFF completely.
This RFC assumes a Mayfly 1.1  MCP1665T-E referenced to V_BATT for discussion and able to supply 620mA.   

The Modbus/RS485 can interface with a number of instruments proposed 4 ( 3 RS485 + 1SDI-12) at 12V.   
Each instrument may consume 55mA active current,  https://github.com/EnviroDIY/YosemitechModbus#power-supply
and periodically surge to 300mA for 10mS   
For 4 instruments this is 220mA + surge to 520mA - within 620mA measured.   
Its proposed that the working temperature for the Mayfly wingboard outdoors environment 10C to 60C.   
Its proposed that each instrument interface "facility" be fused seperately, so that a short on one facility only disables that instrument.   
By having each instrument fused sperately, the analog fuse can be better rated to fit with the available power. See schematic.   

https://github.com/EnviroDIY/Mayfly-Modbus-Wing/issues/3

The power availability needs to be determined through a Batter Management algorithim before any demand is placed on the battery.

## New Rev 8 features under discussion
- instrument power "12V" has 155mA resetable fuse (PTC), limiting power drawn on a line short per external customer line - rev8 is add fuse to each seperate instrument power.

- ideally each instrument power would be powered through a seperate power switch (4). However ModularSensors software (0.32.2) currently turns all sensor power on in parrallel, and then works through each sensor to perform measurements. If a seperate instrument power switch was produced, it would be to facilitate the change in ModularSensors.  
- battery monitoring, accurate battery (V), energy consumed (mAHrs) and instaneous current mmeasurement - change to accurate battery V measurement only.

## Mayfly Rev 7 wingboard interface requirements unchanged for Rev8 are   
- One RS485 interface on three physical connectors for wiring, all clearly labeled with G V B A   
- One SDI-12 interface on one physical connector for wiring, clearly labeled with V G D  
- SDI-12 board has a proto type capability to level shift to a true +5V. This requires intergration and testing with SDI-12 software. 
- RS485 activity indication - a dual line status LED Red/Green a) Red flashes for transmit to RS485 instrument, B) Green flashes from RS485 instrumnent (response)  
- local RS485 120ohm termination 2mm jack. The installer can activate the RS485 120ohm termination resistor as needed.   
- TTL lines have power safe data lines.  (not tested)
- ground wire. Connect the RS485 GND through a thick 18AWG wire to external ground to conduct external power surges. 
- pcb designed KiCad open source https://kicad.org/ easier to modify. Thanks to the previouse generation of board, that was imported into KiCad. 

## Mayfly Power Management Discussion  

For standalone solar powered operation, the ability to supply power depends on there being available power. For 4 instruments, its assumed this demand could be 220mA for 10seconds and up to 620mA for less than 10mS.    
There is only likely to be guaranteed power at all times of the day, if the Li-Po rechargeable battery has stored enough.    

This requires a battery management algorithm to detect if there is adequate power. For a specific battery capacity, the algorithm should for a requested load demand, answer can that load be sourced. This algorithm needs to use a provisioned battery capacity and battery measurement, typically battery V, to determine if the answer is TRUE/(Yes there is capacity)or FALSE/(no there isn’t enough capacity).    
The Mayfly 1.1 Mega1284 option with A6 measuring battery voltage via 10bitADC referenced to Vcc=3.3V for measuring battery voltage is NOT accurate or linear enough around a working threshold between 3.8V and 4.0V to be useable.   

How to accurately measure the battery voltage is a wip. A) investigate using internal VREF, good for stability but might still need calibration B) Could possibly use LC709204F. Rev 7 used an LTC3100.    

The connector supplied V_BAT is part of the Mayfly 1.1 bq24074 “Battery Charger” with dynamic power management. The V_BAT dynamic power management voltage is slightly less than the battery when discharging, and when the battery is charging is 200mV more. There is no way of detecting when the battery is charging or if it is charging    






 
