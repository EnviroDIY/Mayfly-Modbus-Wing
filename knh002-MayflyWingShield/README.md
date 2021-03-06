# Mayfly Modbus wingboard

This is a build it yourself wingboard, or can be configured and order from Macrofab.com (ordering 50pcs is best cost tradoff).     
In building hardware, it needs testing and tracking. Typically add a serial number (or in this case read the internal one, and print it on the board) and then step it through the basic hardware functional tests.    

This Mayfly wingboard interface supports   
- One RS485 interface on three physical connectors for wiring, all clearly labeled with G V B A   
- One SDI-12 interface on one physical connector for wiring, clearly labeled with V G D  
- improved brightness - a dual line status LED Red/Green a) Red flashes for transmit to RS485 instrument, B) Green flashes from RS485 instrumnent (response)  
- improved power supply to a minimium 1.9W continuous. The output voltage is dependent on booster selected. The boost circuit may be able to supply larger surge currents as it's supply climbs/boosts as the LiIon bat is low impedance. These could be as high as 0.7A continuous at lower voltages, valuable for starting up instruments on turnon.
- new feature - Powering routing that allows connection directly to the LiIon battery, for more efficent generation and power resilency.  
   For the power output "12V" current supply, the current supplied can be 155mA to 250mA surge (1.9W continuous/3Wsurge) depending on boost hybrid.  
   Low ESR capacitor close to the booster supply input pins to avoid power surge propagation
- new feature - instrument power "12V" has 155mA resetable fuse (PTC), limiting power drawn on a line short.
- new feature - local RS485 120ohm termination 2mm jack. The installer can activate the RS485 120ohm termination resistor as needed.   
- new feature - battery monitoring, accurate battery (V), energy consumed (mAHrs) and instaneous current mmeasurement.
- new feature - TTL lines have power safe data lines.  (not tested)
- new feature - ground wire. Connect the RS485 GND through a thick 18AWG wire to external ground to conduct external power surges. 
- new feature - pcb designed KiCad open source https://kicad.org/ easier to modify. Thanks to the previouse generation of board, that was imported into KiCad. 

Changes from the old Mayfly RS485 wingboard    
a) doesn't use the unmanaged hybrid.  These hybrids where of unspecified quality, and often didn't work for me. 

Building Guide - to-be-updated - see    
https://github.com/neilh10/SensorModbusMaster/wiki    
https://github.com/EnviroDIY/YosemitechModbus#suggested-setup-with-an-envirodiy-mayfly


# KNH002 Circuit Discription
(as per rev7) 
- This has one RS485 circuit with three connectors, and one SDI-12 with a boost converter typically 12V.
- Typically the 4 physical connections are decided by the end-user, and soldered onto the board. The silk screen has clearly printed both side for three RS485 connectors G V B A  and one SDI-12 V G D. 
  Where G=Gnd  V=Voltage and then RS485 designators A and B and SDI-12 D     
- Typically the boost hybrid is decided by the end user. BOM has 12V option.   
- U1/ MAX22025 is the RS485 physical interface, and manages the RS485 half-duplex state machine.   
- R2/R3 649ohms are the pull-up down resistors for a long twisted pair transmission line. The value 649ohms was selected based on a remote 120Ohm and local 120ohm terminations. Its optomized for a single instrument termination over a long line, but typically will work with a number instruments terminating locally. Its up to the user to figure out if a complex set of instruments and wire lengths works for them, or do RS485 line calculations for what value of resistors they need.     
- The Max22025 Rxb (Rx buffered) interfaces to the Mafyly via the Ioff Safe SN74LVC234. These allow the MAX22025/SN74LVC234 to be turned off, and not take current from the Mayfly.   
- The Green LED is attached to the Rxb driven by the Max22025 output. When an instrument responds to a poll request, the green LED turns ON when signal is low and powered. Statastically its likely there will be some low portions in the received signal. The green is high intensity to be visible.
- The SN74LVC234 Txb Tx(buffered) drives the RED LED, turning ON when LOW, and board powered. This reflects attempts to poll a target instrumnet. ModularSensors code attempts multiple (3) times to contact an instrument. For the sensor set to read/average 3 times, then for a disconnected instrument there will be3 sets of fast red flashes. 
- SDI-12 is driven from D7, and has surge circuitry (FUTURE: cct description to be supplied but it is very simple).
- Typically the power will be supplied by a battery attached to the J8 labelled "Bat", and a jumper wire will supply power from J9 labelled "May" . On the underside of the board these are clearly labeled "Mayfly" and "Battery" with the + connection identified.   
- Typically the power will be boosted by U2, and C1 is designed to be able to supply fast switching currents on U2. For detailed analysis refer to the capacitor specification and how switching circuits work.    
- The power is switched via U3 Load Switch. A SIP32431 or MIC94072YC6 or similar part. This turns ON when 3Vsw is activated by the Mayfly software.  The load switch can support continuous 1.4A. If there is a short circuit in the power loop (before the fuse or if the fuse is bypassed), this devices will emit smoke and become non-operational.   
- The output of U2 has an electronic (automatic reset) fuse R8 155mA, protecting from a short on the output "12V".    
-  Fuses are rated at minimium holding current, and guarenteed trip at 350mA. After it trips, when the short circuit is removed it automatically resets. If output wire is short circuit it will protect battery/processor reliability.  
      (The old RS485 hybrid wingboard used the builtin Mayfly regulator that would limit current flows at 3.3V to 0.5A, also limiting power availability to max 1.65W)   
- On building a board, it can be built with power coming from the 5Vsw, by adding R1=0 and not stuffing U3,J8 J9 (and of course U5 or R5)    
- Battery coloumb "Fuel Gauge" monitor - STC3100. This supplies acurrate Liion Battery voltage, coloumb in mAh with a typical 0.2mAh resolution, 8-byte unique ID, 32Ram bytes backed up by LiIon battery. There is a device driver https://github.com/neilh10/STC3100arduino and a ModularSensors "STSTC3100_Sensor".
 

# History   
https://github.com/neilh10/SensorModbusMaster/issues/1    
https://github.com/EnviroDIY/SensorModbusMaster/issues/14 

 # KNH002 Circuit Discription  rev7 additions rev6
For rev 7 1) adds the SDI-12 circuit accessible through 3wire interface 2) changes the STC3100 to be a TSSOP8 device that can be hand soldered and is available.
This lengthens the board to 2.1" from rev 6 length of 1.7".
SDI-12 Host is challenging to test with a number of different devices. A seperate device test is needed to verify interoperability.    

2021-Mar-10 KNH002rev6 update from Rev4 (Revision 5 had one device different and never tested)     
   The 12V fuse is 155mA hold, 330mA Trip changed from previous 180mA. A line short causes the heating in the electronic fuse, which causes it to go to high resistance, limiting the current flow.       
   The LED resistor for the Green is set to 60ohms make the emitted mcd similar to the Red Led which is 120ohms. For succesful sensor poll, a user should see the Red/Green flashes, For unsucesfull polling with no response will see a Red Flash.  
   A technical detail, the PCB footprint Rev6 is correct, on the Rev4 it had an error, swapping the colours.
   The connector labeled "May" is physically closest to the Mayfly bat Jp1 and polarised to be the same as Mayfly JP1. Rev4 had it the other way around.      
   The battery monitoring IC STC3100  monitors the LiIon battery Voltage, Current and power used mAhr/coloumbs. This was introduced in Rev5, Rev4 had a different IC that became unavailable.     
   Rev6 PCB https://oshpark.com/shared_projects/tlFl6OPg    
   https://github.com/neilh10/SensorModbusMaster/tree/release1/hardware/knh002-MayflyWingShield/rev6    
   

2021-Feb-10 KNH002revision4 https://github.com/neilh10/SensorModbusMaster/tree/release1/hardware/knh002-MayflyWingShield 
    All boards and libs are in  KNH002rev4_2102101316published.zip  (as of Mar10 not recommended as has a number of issues corrected in rev6)

Background (from original Mayfly Wingboard)    

The [Modbus RTU](https://en.wikipedia.org/wiki/Modbus) *logical* communication protocol is typically transmitted using the *physical* [RS-485](https://en.wikipedia.org/wiki/RS-485) serial communication signaling standard.

The Mayfly micro-controllers,  transmit serial signals using transistor-to-transistor logic, or [TTL serial](https://learn.sparkfun.com/tutorials/serial-communication/wiring-and-hardware).

Sensors with Modbus will need to an RS485-to-TTL adapter module to communicate. Modbus sensors typically rated to be able to use 8-30V.  12V usally covers modst Modbus sensors - see each sensor specification. This step up Voltage converter is provided on board. 
Last, the electrical "logic level" signals returned from the board must be compatible with the Mayfly's 3.3V logic and not take power when the RS485 circuits are powered down.

- Early DIY prototype:
  https://github.com/EnviroDIY/YosemitechModbus#suggested-setup-with-an-envirodiy-mayfly
