AVR ATtiny microcontroller family is a really interesting and compact platform supported by PJON [Arduino compatible implementation](https://github.com/gioblu/PJON) and soon also by [PJON_ASK wireless implementation](https://github.com/gioblu/PJON_ASK). 

####How to program ATtiny 45/85
You physically need at least one ATtiny microcontroller, a breadboard, some jumpers and an Arduino duemilanove / Uno used as an Arduino ISP programmer. Follow [High-Low Tech tutorial](http://highlowtech.org/?p=1706) by David Mellis and get the last version of the [attiny repository](https://github.com/damellis/attiny).

####Use PJON with ATtiny45/85
PJON [Arduino compatible implementation](https://github.com/gioblu/PJON) works smoothly on ATtiny45/85 8Mhz (internal) with pin 2 and 3.  

####Use PJON with ATtiny45/85 with external oscillator
Because of the internal clock's lack of precision, with some ATtiny85 in particular, low communication performance can be detected; extended tests proven the ATtiny internal clock to be extremely inaccurate (timing inconsistency between two identical ATtiny85 can be detected). Here is an example how it works with external 16 MHz oscillator.

![image](https://cloud.githubusercontent.com/assets/6849527/21525671/0e9eab1e-cd1f-11e6-8e42-b9396dffc8d6.png)

```
// Arduino UNO sketch
#include <PJON.h>
PJON<SoftwareBitBang> bus(1); // <Strategy name> bus(selected device id)
#define LED 13
void setup() {
  pinModeFast(LED, OUTPUT);
  digitalWriteFast(LED, LOW);
  bus.set_shared_network(false);
  bus.strategy.set_pin(12);
  bus.include_sender_info(false);
  bus.begin();
}
void loop() {  
  digitalWrite(LED, HIGH);
  delay(30);
  digitalWrite(LED, LOW);
  bus.send_packet_blocking(2, "B",1);
  delay(30);
};

```

```
// Attiny85 sketch
#include <PJON.h>
PJON<SoftwareBitBang> bus(2); // <Strategy name> bus(selected device id)
#define LED 0
void setup() {
  pinModeFast(LED, OUTPUT);
  digitalWriteFast(LED, LOW);
  bus.set_shared_network(false);
  bus.strategy.set_pin(2);
  bus.include_sender_info(false);
  bus.begin();
  bus.set_receiver(receiver_function);
};
void receiver_function(uint8_t *payload, uint8_t length, const PacketInfo &packet_info) {
  digitalWrite(LED, HIGH);
  delay(30);
  digitalWrite(LED, LOW);
}
void loop() {
  bus.receive(1000);
};

```
