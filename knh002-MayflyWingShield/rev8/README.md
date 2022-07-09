# Mayfly Modbus wingboard PROPOSED RFC  Rev8 design

This is a Request For Comment (RFC) on design schematic knh002r8 schematicRs485_220708review.pdf at https://github.com/EnviroDIY/Mayfly-Modbus-Wing/tree/master/knh002-MayflyWingShield/rev8    
Comments for the design are opened on 2022-July-9th will be closed Monday 2022-July-20 12noon PST.      

The Modbus/RS485 can interface with a number of physical lines6 RS485 + 1SDI-12, at 12V.   
Requirement: Supply  12V +/ 5% @0.5A or 6W across temperature and battery voltage 3.8V to 4.2V    
Objective : Smooth a 290mA Surge for 10mS.
Objective: Design for a working temperature for outdoors environment -10C to 60C. 
Requirement: Power (nominal 3.8V) 2 * 2pin JST    
Requirement: 6 (3+3) RS485 ports – 4pin screw term, & optional 4pin grove 2mm   
Optional : 4pin grove may be optionally assembled after final testing   
Objective: Board break off of 3 RS485 ports to shorten.   
Requirement : RS485 2wire (UART Tx & Rx) with auto-direction   
Requirement : RS485 UART Tx activity, command to the equipment, indicated with LED RED visible to the eye at 19200baud or less   
Requirement : RS485 UART Rx activity, response from the equipment, indicated with LED GREEN visible to the eye at 19200baud or less   
Requirement: 2 Physical SDI-12 ports – both 3pin screw term, & optional 3pin grove 2mm  
Requirement: SDI-12 has "SDI-12 standard" ESD   
Requirement : All physical ports fused @25c hold 155mA    
Objective : Reliable Physical Tie in - can’t unseat itself. Use mayfly compatible sub-panel.   
Requirement : Mayfly interfaces at 3.3V and Ioff Safe    

A use case is described, where each instrument may consume 55mA active current,  https://github.com/EnviroDIY/YosemitechModbus#power-supply
and periodically surge to 290mA for 10mS   
For 6 instruments this is 340mA (within 500mA budget) + periodic 10mS surge 290mA .   
  
Each instrument interface "facility" is fused seperately, so that a short on one facility only disables that instrument.   
Two or more line shorts may cause excessive current draw, and may not cause either fuse to operate.      
By having each instrument fused sperately, the analog fuse can be better rated to fit with the available power.   

The discussion on specification for this board is per 
https://github.com/EnviroDIY/Mayfly-Modbus-Wing/issues/3

The power availability needs to be determined through a Battery Management algorithim before any demand is placed on the battery. https://github.com/EnviroDIY/EnviroDIY_Mayfly_Logger/issues/32

The Mayfly rev 1.1 nominnal 12V is actually 11.3V @100mA (nominal +12V) and is derived via +5V to be able to turn it off completely. 

## New Rev 8 features 
- instrument power "12V" has 180mA @25C resetable fuse (PTC), limiting power drawn on a line short per external customer line - rev8 is add fuse to each seperate instrument power.   
- expanded number of physical connectors    
-  changed RS485 chip with auto-direction due to unavailability of rev7 chip    
-  removed battery monitoring capability   
 
 Related Issues    
- battery monitoring, need an accurate low noise battery V measurement.    
https://github.com/EnviroDIY/EnviroDIY_Mayfly_Logger/issues/32   
https://github.com/EnviroDIY/EnviroDIY_Mayfly_Logger/issues/36    

This is possible through a filtered Vbat A4, or external LiIon measurement such as https://adafruit.github.io/Adafruit_LC709203F/html/index.html      
https://github.com/neilh10/ModularSensors/issues/75    
https://github.com/neilh10/ModularSensors/issues/107     

## Mayfly Rev 7 wingboard interface requirements unchanged for Rev8 are   
- RS485 interface labeled with G V B A   
- SDI-12 interface on one physical connector for wiring, clearly labeled with V G D  
- local RS485 120ohm termination 2mm jack. The installer can activate the RS485 120ohm termination resistor as needed.   
- ground wire. Connect the RS485 GND through a thick 18AWG wire to external ground to conduct external power surges. 
- pcb designed KiCad open source https://kicad.org/ easier to modify. Thanks to the previouse generation of board, that was imported into KiCad. 

## Mayfly Power Management Discussion  

For standalone solar powered operation, the ability to supply power depends on there being available power. For 4 instruments, its assumed this demand could be 220mA for 10seconds and up to 620mA for less than 10mS.    
There is only likely to be guaranteed power at all times of the day, if the Li-Po rechargeable battery has stored enough.    

This requires a battery management algorithm to detect if there is adequate power. For a specific battery capacity, the algorithm should for a requested load demand, answer can that load be sourced. This algorithm needs to use a provisioned battery capacity and battery measurement, typically battery V, to determine if the answer is TRUE/(Yes there is capacity)or FALSE/(no there isn’t enough capacity).    
The Mayfly 1.1 Mega1284 option with A6 measuring battery voltage via 10bitADC referenced to Vcc=3.3V for measuring battery voltage is NOT accurate or linear enough around a working threshold between 3.8V and 4.0V to be useable.   

How to accurately measure the battery voltage is a wip. A) investigate using internal VREF, good for stability but might still need calibration B) Could possibly use LC709204F. Rev 7 used an LTC3100.    

The connector supplied V_BAT is part of the Mayfly 1.1 bq24074 “Battery Charger” with dynamic power management. The V_BAT dynamic power management voltage is slightly less than the battery when discharging, and when the battery is charging is 200mV more. There is no way of detecting when the battery is charging or if it is charging    






 
