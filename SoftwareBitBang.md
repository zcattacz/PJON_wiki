

####Compatibility
- ATmega88/168/328 8/16Mhz (Diecimila, Duemilanove, Uno, Nano, Mini, Lillypad)
- ATmega2560 16Mhz (Arduino Mega)
- ATmega16u4/32u4 (Arduino Leonardo)
- ATtiny45/85 8/16Mhz (Trinket, other ATtiny 85 boards), see [ATtiny Wiki page](https://github.com/gioblu/PJON/wiki/ATtiny-interfacing)
- SAMD (Arduino Zero)
- ESP8266 v.1-7 80Mhz "AI-THINKER AT" firmware, see [ESP8266 Arduino IDE](https://github.com/esp8266/Arduino)  
- ESP8266 NodeMCU v0.9-1.0 80Mhz, see [ESP8266 Arduino IDE](https://github.com/esp8266/Arduino)  

####Performance
PJON works in 3 different communication modes, `STANDARD`, `FAST` and `OVERDRIVE`:
- `STANDARD` mode runs at 16.944kBd or 2.12kB/s full cross-architecture / promiscuous clock compatible.
- `FAST` mode runs at 25.157kBd or 3.15kB/s full cross-architecture / promiscuous clock compatible.
- `OVERDRIVE` mode runs a specific architecture at its maximum limits (non cross-architecture compatible). Every architecture has its own limits, Arduino Duemilanove for example runs at 33.898kBd, Arduino Zero can reach 48.000kBd.

When including and using SoftwareBitBang, as physical layer of a PJON bus, you have the complete access to the microntroller ready to be used, as usual, untouched. This happens because SoftwareBitBang is completely software emulated strategy with a non blocking implementation, transforming a painfull walk to the hill in a nice flight.

Single wire simplicity let you to experiment quickly and with creativity. The first test I suggest, at your own risk, is to let two arduino boards communicate through your body touching with the left hand the digital port of the first board (5v 40ma, harmless) and with the right the port of the other one. Will be stunning to see high accuracy digital communication running inside a living biological body. This opens the mind to possible creative solutions.

####How to use SoftwareBitBang
Pass the ```SoftwareBitBang``` type as PJON template parameter to instantiate a PJON object ready to communicate in this Strategy. All the other necessary information is present in the general documentation.
```cpp  
  PJON<SoftwareBitBang> bus; // One wire software emulated physical layer
```

####Why not interrupts?
Usage of libraries is really extensive in the Arduino environment and often the end user is not able to go over collisions or redefinitions. Very often a library is using hardware resources of the microcontroller as timers or interrupts, colliding or interrupting other libraries. This happens because in general Arduino boards have limited hardware resources. To have a universal and reliable communication medium in this sort of environment, software emulated bit-banging, is a good, stable and reliable solution that leads to "more predictable" results than interrupt driven systems coexisting on small microcontrollers without the original developer and the end user knowing about it.