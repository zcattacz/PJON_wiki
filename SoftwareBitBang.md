# Software BitBang

**Medium:** Wire |
**Pins used:** 1 / 2

SoftwareBitBang is the default physical layer strategy used by the PJON template object. This implementation is based on `micros()` and `delayMicroseconds()`. It makes no use of dedicated timers or interrupt driven strategies to handle binary communication. It is designed to have a small footprint on memory and to be extremely resilient to interference and timing inaccuracies. Thanks to the use of a dedicated digitalWriteFast library, can be achieved fast and reliable cross-architecture communication through one or two pins.

####Byte transmission
Every byte is prepended with 2 synchronization padding bits and transmission occurs LSB-first. The first is a longer than standard logic 1 followed by a standard logic 0. The reception tecnique is based on finding a logic 1 as long as the first padding bit within a certain threshold, synchronizing to its falling edge and checking if it is followed by a logic 0. If this pattern is recognised, reception starts, if not, interference, synchronization loss or simply absence of communication is detected at byte level.
```cpp  
 __________ ___________________________
| SyncPad  | Byte                      |
|______    |___       ___     _____    |
|  |   |   |   |     |   |   |     |   |
|  | 1 | 0 | 1 | 0 0 | 1 | 0 | 1 1 | 0 |
|_ |___|___|___|_____|___|___|_____|___|
   |
 ACCEPTANCE
```
This adds a certain overhead to information but reduces the need of precise time tuning because synchronization is renewed every byte. All the first padding bit duration minus `ACCEPTANCE` is the synchronization window the receiver has for every incoming byte. If the length of the first padding bit is less than `ACCEPTANCE` the received signal is considered interference.

####How to use SoftwareBitBang
Pass the ```SoftwareBitBang``` type as PJON template parameter to instantiate a PJON object ready to communicate in this Strategy. All the other necessary information is present in the general [Documentation](Documentation).
```cpp  
  PJON<SoftwareBitBang> bus;
```

####Compatibility
- ATmega88/168/328 16Mhz (Diecimila, Duemilanove, Uno, Nano, Mini, Lillypad)
- ATmega2560 16Mhz (Arduino Mega)
- ATmega16u4/32u4 16Mhz (Arduino Leonardo)
- ATtiny45/85 8Mhz, see [ATtiny Interfacing](ATtiny-interfacing)
- SAMD (Arduino Zero)
- ESP8266 v.1-7 80Mhz "AI-THINKER AT" firmware, see https://github.com/esp8266/Arduino
- ESP8266 NodeMCU v0.9-1.0 80Mhz, see https://github.com/esp8266/Arduino
- MK20DX256 96Mhz (Teensy 3.1)

####Performance
PJON works in 3 different communication modes, `STANDARD`, `FAST` and `OVERDRIVE`:
- `STANDARD` runs at 16944Bd or 2.12kB/s cross-architecture, promiscuous clock compatible.
- `FAST` runs at 25157Bd or 3.15kB/s cross-architecture, promiscuous clock compatible.
- `OVERDRIVE` runs a specific architecture at its maximum limits (non cross-architecture compatible). Every architecture has its own limits, Arduino Duemilanove for example runs at 33898Bd or 4.23kB/s, Arduino Zero can reach 48000Bd or 6.00kB/s.

When including and using SoftwareBitBang, as physical layer of a PJON bus, you have the complete access to the microntroller ready to be used, as usual, untouched. This happens because SoftwareBitBang is completely software emulated strategy with a non blocking implementation, transforming a painfull walk to the hill in a nice flight.

Single wire simplicity let you to experiment quickly and with creativity. The first test I suggest, at your own risk, is to let two arduino boards communicate through your body touching with the left hand the digital port of the first board (5v 40ma, harmless) and with the right the port of the other one. Will be stunning to see high accuracy digital communication running inside a living biological body. This opens the mind to possible creative solutions.

####Why not interrupts?
Usage of libraries is really extensive in the Arduino environment and often the end user is not able to go over collisions or redefinitions. Very often a library is using hardware resources of the microcontroller as timers or interrupts, colliding or interrupting other libraries. This happens because in general Arduino boards have limited hardware resources. To have a universal and reliable communication medium in this sort of environment, software emulated bit-banging, is a good, stable and reliable solution that leads to "more predictable" results than interrupt driven systems coexisting on small microcontrollers without the original developer and the end user knowing about it.


![PJON - Michael Teeuw application example](http://33.media.tumblr.com/0065c3946a34191a2836c405224158c8/tumblr_inline_nvrbxkXo831s95p1z_500.gif)

PJON application example made by the user [Michael Teeuw](http://michaelteeuw.nl/post/130558526217/pjon-my-son)
