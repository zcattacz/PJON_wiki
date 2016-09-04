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
Istantiate a PJON object defining its bus id:
```cpp  
  uint8_t bus_id[4] = {1, 2, 3, 4};
  PJON<> bus(bus_id, 44);  
```
