![PJON wiki](http://www.gioblu.com/PJON/PJON-mars.jpg)

PJON (Padded Jittering Operative Network) is an Arduino compatible, multi-master, multi-media communications bus system. It proposes a Standard and it is designed as a framework to ease digital communication. Thanks to a data link layer agnostic approach the PJON protocol layer is able to communicate data regardless of the medium and the procedure used thanks to the [Strategies](https://github.com/gioblu/PJON/wiki/Strategies). Its more common applications are in the field of internet of things and embedded systems. Extended tests proved its effectiveness on different media like electricity, radio and light. See the [video introduction](https://www.youtube.com/watch?v=vjc4ZF5own8) to have a brief overview.


####PJON Protocol layer specification
- [PJON protocol specification v0.1](https://github.com/gioblu/PJON/blob/master/specification/PJON-protocol-specification-v0.1.md)
- [PJON protocol specification v0.2](https://github.com/gioblu/PJON/blob/master/specification/PJON-protocol-specification-v0.2.md)
- [PJON protocol specification v0.3](https://github.com/gioblu/PJON/blob/master/specification/PJON-protocol-specification-v0.3.md)
- [PJON protocol specification v1.0](https://github.com/gioblu/PJON/blob/master/specification/PJON-protocol-specification-v1.0.md)
- [PJON protocol dynamic addressing specification v0.1](https://github.com/gioblu/PJON/blob/master/specification/PJON-dynamic-addressing-specification-v0.1.md)
- [PJON protocol acknowledge specification v0.1](https://github.com/gioblu/PJON/blob/master/specification/PJON-protocol-acknowledge-specification-v0.1.md)

####Padded jittering data link layer specification
- [Padded jittering data link layer v0.1](https://github.com/gioblu/PJON/blob/master/strategies/SoftwareBitBang/specification/padded-jittering-protocol-specification-v0.1.md)

####Compliant tools
- [saleae-pjon-protocol-analyzer](https://github.com/aperepel/saleae-pjon-protocol-analyzer) by Andrew Grande
- [PJON-python](https://github.com/Girgitt/PJON-python) by Zbigniew Zasieczny
- [ModuleInterface](https://github.com/fredilarsen/ModuleInterface) by Fred Larsen

### Why PJON?
PJON is a tool created to simplify communication between devices and network engineering. Choose the medium you prefer, build your own network of devices and make it work with few lines of code. There are 5 strategies available to communicate data with PJON on various media:

AnalogSampling is designed to sample digital data using analog readings. It can be used to communicate data wirelessly, through any sort of radiation trasducer but it is optimized for light. It was implemented it in the far 2011 as a stand-alone physical layer able to use a couple of LEDs as wireless transceivers connecting two devices in half-duplex mode with a range of maximum 5 meters with ideal conditions. It always remained at experimental stage, here you can see the first video documented experiment. It has been debugged and updated to act as a PJON Strategy and work nominally with a single bidirectional transceiver (a single LED for both reception and transmission) or using separate pins for transmission and reception, to enable more standard use cases.

**[AnalogSampling](https://github.com/gioblu/PJON/tree/master/strategies/AnalogSampling)** | **Medium:** LED/LED and Photodiode

AnalogSampling is designed to sample digital data using analog readings. It can be used to communicate data wirelessly through light. It is optimized to use a single LED for both emitting and sensing and so enable bidirectional communication with a single pair of LEDs (visible light, infrared or ultraviolet) or with two pairs of any sort of tuned photo-emitters and photo-receivers.

**[EthernetTCP](https://github.com/gioblu/PJON/tree/master/strategies/EthernetTCP)** | **Medium:** Ethernet port, wired or WiFi

With the EthernetTCP PJON strategy, multiple devices with Ethernet ports can use PJON to communicate with each other on a LAN, WAN or across the Internet.

**[LocalUDP](https://github.com/gioblu/PJON/tree/master/strategies/LocalUDP)** | **Medium:** Ethernet port, wired or WiFi

With the LocalUDP PJON strategy, multiple devices with Ethernet ports can use PJON to communicate with each other on a local subnet, wired or over WiFi or both.

**[OverSampling](https://github.com/gioblu/PJON/tree/master/strategies/OverSampling)** | **Medium:** Radio, Wire |
**Pins used:** 1 or 2

Oversampling strategy comes from the [PJON_ASK](https://github.com/gioblu/PJON_ASK) repository, and it was integrated in the PJON repository from version 3.0 beta, as a data link layer strategy. Bits are over-sampled to have high resilience in high interference scenarios, like using an ASK/FSK cheap radio transceivers in an urban environment. It is tested effectively with many versions of the ASK/FSK 315/433Mhz modules available on the market with up to 5km range, but it works nominally also through wires and the human body.

**[SoftwareBitBang](https://github.com/gioblu/PJON/tree/master/strategies/SoftwareBitBang)** | **Medium:** Wire | **Pins used:** 1 or 2

SoftwareBitBang is the default data link layer strategy used by the PJON template object. This implementation is based on `micros()` and `delayMicroseconds()`. It makes no use of dedicated timers or interrupt driven strategies to handle communication. It is designed to have a small memory footprint and to be extremely resilient to interference and timing inaccuracies. Thanks to the use of a dedicated digitalWriteFast library, can be achieved fast and reliable cross-architecture communication through one or two pins.

**[ThroughSerial](https://github.com/gioblu/PJON/tree/master/strategies/ThroughSerial)** | **Medium:** Hardware Serial port |
**Pins used:** 2

With ThroughSerial data link layer strategy, PJON can run through a software emulated or hardware Serial port. Thanks to this choice it is possible to leverage of virtually all the arduino compatible serial transceivers, like RS485, radio or infrared modules, still having PJON unchanged on top.

### Practical test
After selecting the medium to be used you can start to build your personal bus (that can be made by only two devices for testing):
```cpp  
 Arduino UNO        Arduino UNO
  _________   wire   _________
 | ||   |_||___     |         |
 ||       ||   |    ||       ||
 ||       ||   |    ||       ||
 ||       ||   |____||       ||
 |_________|        |_|_|__||_|
                      
```
A simple entry level test can be to setup a couple of Arduino Duemilanove / Uno boards connected together with one single wire on both pins 12 as described in the illustration above. When the devices are wired it is possible to test their connectivity sending a packet from device 1 to device 2 and see if device 2 receives it blinking a LED:
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
PJON<SoftwareBitBang> bus(2); // <Strategy name> bus(selected device id)

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
On the receiver side is declared a receiver function that is called when a packet for the device is received. In this case, the function checks if the received character is "B" and if so, blinks the LED connected to pin 13. You should see the receiver device blinking every second.