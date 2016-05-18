
![PJON Strategies wiki](http://www.gioblu.com/PJON/PJON-clouds.jpg)

PJON codebase uses strategies to physically communicate through the medium used, abstracting the physical layer from its procedure. A Strategy is only a class containing a set of functions able to send and receive messages:

- `can_start(uint8_t input_pin, uint8_t output_pin)`
  Returns true if the medium is free and false if the medium is in use.

- `send_byte(uint8_t input_pin, uint8_t output_pin)`
  Sends a byte 

- `receive_byte(uint8_t input_pin, uint8_t output_pin)`
  Receives a byte 

- `get_response(uint8_t input_pin, uint8_t output_pin)`
  Receives acknowledge of correct packet sending

You can define your own set of 4 methods to use PJON with your personal strategy on the media you prefer. Two pins are passed to the methods, enabling twisted pair, serial or radio transceiver physical layer strategies. If you need other functions, variables or constants, those can be defined in your personal Strategy class. Other communication protocols could be used inside those methods to transmit data.

```cpp
// Simple Serial physical layer example
static inline __attribute__((always_inline))
void send_byte(uint8_t b, uint8_t input_pin, uint8_t output_pin) {
  Serial.print(b);
}
```

####How to define a new strategy
To define your new strategy you have only to create a new folder named for example `YourStrategyName` in `strategies`
directory and write the necessary file `YourStrategyName.h`:

```cpp
class YourStrategyName {
  public:
    boolean can_start() { ... };
    uint16_t receive_byte(uint8_t input_pin, uint8_t output_pin) { ... };
    void send_byte(uint8_t b, uint8_t input_pin, uint8_t output_pin) { ... };
    uint16_t get_response(uint8_t input_pin, uint8_t output_pin) { ... };
}
```

Simply add your code in the functions declaration shown above and instantiate PJON using the strategy type you
have created: `PJON<YourStrategyName> bus();`.
