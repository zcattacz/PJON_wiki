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