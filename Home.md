![PJON wiki](http://www.gioblu.com/PJON/PJON-mars.jpg)

PJON (Padded Jittering Operative Network) is an Arduino compatible, multi-master, multi-media communications bus system. It proposes a Standard and it is designed as a framework to ease digital communication. Thanks to a physical layer data link layer agnostic approach the PJON protocol layer is able to communicate data regardless of the medium and the procedure used thanks to the [Strategies](https://github.com/gioblu/PJON/wiki/Strategies). Its more common applications are in the field of internet of things and embedded systems. Extended tests proved its effectiveness on different media like electricity, radio and light.

### PJON Protocol layer specification
- [PJON protocol layer v0.1](https://github.com/gioblu/PJON/blob/master/specification/PJON-protocol-specification-v0.1.md)
- [PJON protocol layer v0.2](https://github.com/gioblu/PJON/blob/master/specification/PJON-protocol-specification-v0.2.md)
- [PJON protocol layer v0.3](https://github.com/gioblu/PJON/blob/master/specification/PJON-protocol-specification-v0.3.md)

### Padded jittering data link layer specification
- [Padded jittering data link layer v0.1](https://github.com/gioblu/PJON/blob/master/strategies/SoftwareBitBang/specification/padded-jittering-protocol-specification-v0.1.md)

### Why PJON?
This PJON implementation is a tool created to simplify devices communication and network engineering. It is made to let you make your personal network of devices choosing the medium you prefer, writing few lines of code. For now there are 3 different strategies to communicate data with PJON on various media.

**SoftwareBitBang** | **Medium:** Wire | **Pins used:** 1 or 2 | [readme](https://github.com/gioblu/PJON/tree/master/strategies/SoftwareBitBang)

SoftwareBitBang is the default data link layer strategy used by the PJON object. This implementation is based on `micros()` and `delayMicroseconds()`. It makes no use of dedicated timers or interrupts to handle communication. It is designed to have a small footprint on memory and to be extremely resilient to interference and timing inconsistencies. Thanks to the use of a dedicated digitalWriteFast library, can be achieved fast and reliable cross-architecture communication through one or two pins. 


**OverSampling** | **Medium:** Radio, Wire |
**Pins used:** 1 or 2 | [readme](https://github.com/gioblu/PJON/tree/master/strategies/OverSampling)

Oversampling strategy comes from the [PJON_ASK](https://github.com/gioblu/PJON_ASK) repository, and it was integrated in the PJON repository from version 3.0 beta, as a physical layer strategy. Bits are oversampled to have high resilience in high interference scenarios like ASK/FSK cheap radio transceivers in urban environment. It is tested effectively with many versions of the ASK/FSK 315/433Mhz modules available on the market. 

**ThroughHardwareSerial** | **Medium:** Hardware Serial port |
**Pins used:** 2 | [readme](https://github.com/gioblu/PJON/tree/master/strategies/ThroughHardwareSerial)

ThroughHardwareSerial digital communication transport layer used as a Strategy by the PJON framework (included in version v4.1) With ThroughHardwareSerial PJON can run through the Serial port of your device. Both ports should be free from usb computer connection and data pin should be wired inverted (TX->RX, RX->TX). 

### Practical test
Once you have chosen the medium you will use you can start to build your personal bus (that obviously could be made also by only two devices for testing):
```cpp  
    _______     _______     _______     _______     _______
   |       |   |       |   |       |   |       |   |       |  
   | ID 0  |   | ID 1  |   | ID 2  |   | ID 3  |   | ID 4  |  
   |_______|   |_______|   |_______|   |_______|   |_______|    
 ______|___________|___________|___________|___________|______
          ___|___     ___|___     ___|___     ___|___
         |       |   |       |   |       |   |       |   
         | ID 5  |   | ID 6  |   | ID 7  |   | ID 8  |
         |_______|   |_______|   |_______|   |_______|    
```
A simple entry level test can be to setup a couple of Arduino duemilanove / Uno boards connected together with one single wire on both pins 12. If you have the time and will you could connect to the same wire many other devices like in the graph above and run a more complicated test. When the devices are connected it is possible to test their connectivity sending a packet from device 1 to device 2 and see if device 2 receives it blinking a LED:
```cpp
#include <PJON.h>
PJON<SoftwareBitBang> bus(1); // <Strategy name> bus(selected device id)

void setup() {
  bus.strategy.set_pin(12);
  bus.begin();
  bus.send_repeatedly(2, "B", 1, 1000000); // Send B to device 44 every second
}

void loop() {
  bus.update();
};
```
As you can see the code above, device 1 is simply sending a "B" every second to the device id 2.
```cpp
#include <PJON.h>
PJON<SoftwareBitBang> bus(44); // <Strategy name> bus(selected device id)

void setup() {
  pinModeFast(13, OUTPUT);
  digitalWriteFast(13, LOW); // Initialize LED 13 to be off
  bus.strategy.set_pin(12);
  bus.begin();
  bus.set_receiver(receiver_function);
};

void receiver_function(uint8_t *payload, uint8_t length, const PacketInfo &packet_info) {
  if(payload[0] == 'B') {
    digitalWrite(13, HIGH);
    delay(30);
    digitalWrite(13, LOW);
  }
}

void loop() {
  bus.receive(1000);
};
```
On the receiver side is declared a receiver function that is called when a packet for the device is received. In this case, the function checks if the received character is "B" and if so blinks the LED connected to pin 13.