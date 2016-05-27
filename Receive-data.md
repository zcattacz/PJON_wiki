Define a `void function` that will be called if a correct message is received. This function receives 3 parameters: the recipient id (so you can determine if was a `BROADCAST` or device's id addressed packet), the received payload and its length:
```cpp
void receiver_function(uint8_t id, uint8_t *payload, uint8_t length) {
  Serial.print("Recipient id: ");
  Serial.print(id);
  Serial.print(" Content: ");

  for(int i = 0; i < length; i++)
    Serial.print(payload[i]);

  Serial.print(" Length: ");
  Serial.println(length);
};
```
If a bus id is defined, the payload is prepended with the recipient bus id:
```cpp
void receiver_function(uint8_t id, uint8_t *payload, uint8_t length) {
  Serial.print("Recipient id: ");
  Serial.print(id);
  Serial.print(" Recipient bus id: ");
  for(int i = 0; i < 4; i++)
    Serial.print(payload[i]);

  Serial.print(" Content: ");
  for(int i = 4; i < length; i++)
    Serial.print(payload[i]);

  Serial.print(" Length: ");
  Serial.println(length);
};
```
Inform the bus to call `receiver_function` when a correct message is received:
```cpp
bus.set_receiver(receiver_function);
```
To correctly receive data call the `receive` function at least once per loop cycle:
```cpp
int response = bus.receive();
```
If you want to dedicate a certain timeframe to reception call the `receive` function passing the maximum reception time in microseconds:
```cpp
int response = bus.receive(1000);
```