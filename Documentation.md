The first step is the wiring: connect with a wire two boards using a digital pin on both boards. After this you should have both arduino boards connected by the wire on the same pin. The selected pins are the same only for simplicity and to avoid mistakes, PJON works fine on every Arduino digital or analog (used as digital) I/O pin.

Lets start coding, looking how to instantiate in the simplest way the `PJON` object that in the example is called bus:
```cpp  
  PJON<> bus;

  // or 

  PJON<SoftwareBitBang> bus;

```
Now the PJON bus runs with the default strategy SoftwareBitBang, and no id is assigned to the device, still able to communicate using `BROADCAST` id and to try to acquire an id using the `acquire_id` method. If you need to predefine the device id on instantiation, pass the selected id as shown below:
```cpp  
  PJON<> bus(1); // Device id 1
```

Now set the pin where the bus is connected 
```cpp  
bus.set_pin(12); // Set pin where to operate
```

After this the `begin` function has to be called in the setup or in the loop. The lack of this method can lead to collision problems on startup, so be sure of its presence in every device's setup. 
```cpp  
  bus.begin(); // initialize PJON bus
```

If you are interested auto-addressing is really easy to use but still experimental:
```cpp  
  PJON bus;
  bus.set_pin(12);
  bus.begin();
  bus.acquire_id();
  Serial.println(bus.device_id()); // Device id found with scan
```
All ids are scanned sending a packet containing the `ACQUIRE_ID` constant. If no answer is received from an id, it is considered free. If auto-addressing approach is your choice, you should never have a blind timeframe longer than 1.5 seconds (i.e. `delay(2000)`) between every `receive` function call. This constrain is imposed by the necessity of having the device able to receive incoming packets (as `ACQUIRE_ID` used to determine if a device id is free or not). If a device is executing something for too much time while not reading the bus for incoming packets, its device id could be stolen by another device. There is still no device id collision detection / correction, but respecting the described rules, collision should not happen.

#### Transmit data
Data transmission is handled by a packet manager, the `update()` function has to be called at least once per loop cycle:

```cpp  
  bus.update();
```

To send a string to another device connected to the bus simply call `send()` function passing the ID you want to contact, the string you want to send and its length:

```cpp
bus.send(100, "Ciao, this is a test!", 21);
```
I know that the packet length is boring to fill but is there to prevent buffer overflow. If sending arbitrary values `NULL` terminator strategy based on `strlen()` is not safe to detect the end of a string.

To send a value repeatedly simply add as last parameter the interval in microseconds you want between every sending:

```cpp
int one_second_delay_test = bus.send(100, "Test sent every second!", 23, 1000000);
```

`one_second_delay_test` contains the id of the packet. If you want to remove this repeated task simply:

```cpp
bus.remove(one_second_delay_test);
```

To broadcast a message to all connected devices, use the `BROADCAST` constant as recipient ID.

```cpp
int broadcastTest = bus.send(BROADCAST, "Message for all connected devices.", 34);
```

#### Receive data
Now define a `void function` that will be called if a correct message is received. This function receives 2 parameters: the message length and its content.

```cpp
void receiver_function(uint8_t length, uint8_t *payload) {
  Serial.print("Message content: ");

  for(int i = 0; i < length; i++)
    Serial.print((char)payload[i]);

  Serial.print(" | Message length: ");
  Serial.println(length, DEC);
};
```

Inform the bus to call `receiver_function` when a correct message is received:

```cpp
bus.set_receiver(receiver_function);
```

To correctly receive data call `receive()` function at least once per loop cycle passing as a parameter, the maximum reception time in microseconds:
```cpp
int response = bus.receive(1000);
```

####Error handling
PJON is designed to inform the user if an error is detected. A `void function` has to be defined as the error handler, it receives 2 parameters the first is the error code and the second is 1 byte additional data related to the error.

Error types:
- `CONNECTION_LOST` (value 101), `data` parameter contains lost device's id.
- `PACKETS_BUFFER_FULL` (value 102), `data` parameter contains buffer length.
- `MEMORY_FULL` (value 103), `data` parameter contains `FAIL`.
- `CONTENT_TOO_LONG` (value 104), `data` parameter contains content length.
- `ID_ACQUISITION_FAIL` (value 105), `data` parameter contains actual device id.

```cpp
void error_handler(uint8_t code, uint8_t data) {
  if(code == CONNECTION_LOST) {
    Serial.print("Connection with device ID ");
    Serial.print(data);
    Serial.println(" is lost.");
  }
  if(code == PACKETS_BUFFER_FULL) {
    Serial.print("Packet buffer is full, has now a length of ");
    Serial.println(data, DEC);
    Serial.println("Possible wrong bus configuration!");
    Serial.println("higher MAX_PACKETS in PJON.h if necessary.");
  }
  if(code == MEMORY_FULL) {
    Serial.println("Packet memory allocation failed. Memory is full.");
  }
  if(code == CONTENT_TOO_LONG) {
    Serial.print("Content is too long, length: ");
    Serial.println(data);
  }
  if(code == ID_ACQUISITION_FAIL) {
    Serial.print("Can't acquire a free id ");
    Serial.println(data);
  }
}
```

Now inform the bus to call the error handler function in case of error:
```cpp
bus.set_error(error_handler);
```