The device id can be predefined on instantiation:
```cpp  
  PJON<> bus(44);  
```
Can also be set afterwards through a setter:
```cpp  
  bus.set_id(44);  
```
Can be read through a getter:
```cpp  
  bus.device_id();  
```

If you are interested auto-addressing is really easy to use but still experimental:
```cpp  
  PJON bus;
  bus.set_pin(12);
  bus.begin();
  bus.acquire_id();
  Serial.println(bus.device_id()); // Device id found with scan
```
All ids are scanned sending a packet containing the `ACQUIRE_ID` constant. If no answer is received from an id, it is considered free. If auto-addressing approach is your choice, you should never have a blind timeframe longer than 1.5 seconds (i.e. `delay(2000)`) between every `receive` function call. 

This constrain is imposed by the necessity of having the device able to receive incoming packets (as `ACQUIRE_ID` used to determine if a device id is free or not). 

If a device is executing something for too much time while not reading the bus for incoming packets, its device id could be stolen by another device. There is still no device id collision detection / correction, but respecting the described rules, collision should not happen.