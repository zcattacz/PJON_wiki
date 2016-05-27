If you want to communicate bidirectionally on a single medium, so sharing transmission and reception line use:
```cpp  
 PJON<> bus;
 bus.set_pin(12);
```
With this setup is possible to communicate in `HALF_DUPLEX` with up to 254 different devices on the same wire.

If for some reason you need to keep separate the two lines, for example if using cheap radio transmitter and receiver modules, to which you have to connect two wires:
```cpp  
 PJON<OverSampling> bus;
 bus.set_pins(11, 12);
```

If you don't need bidirectional communication and you have only the transmitter on one side and the receiver on the other side you can use the `NOT_ASSIGNED` constant:
```cpp  
 PJON<OverSampling> bus;
 bus.set_pins(11, NOT_ASSIGNED); // Only receiver
```