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