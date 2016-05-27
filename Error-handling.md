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