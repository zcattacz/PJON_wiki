# Device ID Setup

```cpp  
  PJON<> bus; 
  // PJON device with NOT_ASSIGNED id in a local mode

  PJON<> bus(44); 
  // PJON device with id 44 in a local mode

  uint8_t bus_id[4] = {1, 2, 3, 4};
  PJON<> bus(bus_id, 44); 
  // PJON device id 44 in bus id 1.2.3.4 in a shared mode
```
Device id can also be set afterwards through a setter:
```cpp  
  bus.set_id(44);  
```
Can be read through a getter:
```cpp  
  bus.device_id();  
```
