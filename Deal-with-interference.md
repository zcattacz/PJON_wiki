In some situations, interference can affect the communication medium; understanding its source and how to mitigate its effects is a key element to master understanding in electronics and digital communication. Interference affecting a PJON bus can be divided in three different groups:
- Generated by hardware connected to the bus.
- Generated by third-party hardware not connected to the bus or device's power supply.
- Unknown.

The third category describes interference that can occur because of unknown sources, the extremely complicated and interrelated behavior of radiation waves often brings impossible to determine the cause (or the sum of causes) of a disturbing signal.

### Connected hardware related interference
The most experienced sort of interference is a high chance to find the channel `BUSY`. This happens because the pin is "floating" from a logic state to the other one. This often happens because the bus, and the devices connected to it are acting as a capacitor, often reaching the `HIGH` logical state also in a quiescent state. A fast solution to this problem is the use of a pull-down resistor to discharge the unwanted capacitance build-up in the channel. The correct resistor value to be applied is very variable, depending on the topology and medium used, but is often near to the mega ohm order.
```cpp
I/O PIN ------------------- BUS
                      |
GND -------[/\/]------/

           resistor
```
Bandwidth loss related to the system's power consumption is the sign of power supply interference. Never feed devices with shared power supply with high-power demanding appliances like servos, motors or actuators. If this is not possible at least position a diode connected to `VIN`, able to block inverse current from the `VIN` pin back to the wire and use a capacitor to fill temporary feeding shortages.
```cpp
GND ------
         |    
        === capacitor
         |
VIN ---------[|__]------- POWER SUPPLY FEED

             diode
```
The above solution can reduce but not eliminate transient voltage spikes. A more decisive way to deal with transient voltage is to use diodes in opposition. This technique reduces the short duration electrical spikes can occur because of power shortages, power transitions in other large equipment on the same power line or lightning strikes.
```cpp
             diode                       diode

GND ---------[__|]--------------|--------[__|]--- POWER SUPPLY FEED
                                |                
                /-----[/\/]-----/  
                |
                |     resistor
I/O PIN  -------|-------------------------------- BUS                         
```
A serious step toward reliability is to use a well insulated wire (mil standard for radio communication / avionics surplus), but this can only be applied for home-made / non-serial production; a more standard approach is the use of a simple coaxial cable in one of its forms, from earbuds wire to super-expensive gold-plated coaxial. Thanks to the "sock" ground shielding the use of this sort of cable highly reduces interference and is also really comfortable, connecting also the ground with only one wire.

```cpp
 _______                                   _______
|       | I/O PIN                 I/O PIN |       |
|       |\                               /|       |
| ID 1  | > ====== COAXIAL CABLE ====== < | ID 2  |
|       |/                               \|       |
|_______| GND                         GND |_______|
```    

### Third-party hardware related interference
Electromagnetic fields can temporary charge the bus and provoke series of burst-errors. This is often provoked by powerful rotating, magnetic motors, welders, tasers and other devices able to burst a mix of high-power radio waves and magnetic fields. A strong palliative is the use of ferrite beads. Many devices where digital communication is used are equipped with it (see Sony Playstation old wired controller).
```cpp
              _______
             |       |
I/O PIN -----|-------|----- BUS
             |_______|

              ferrite bead
```   