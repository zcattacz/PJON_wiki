Template can be scary at first sight, but they are quite straight-forward and efficient. Lets start coding, looking how to instantiate in the simplest way the `PJON` object that in the example is called bus with a wire compatible physical layer:
```cpp  
  PJON<> bus;

  // or 

  PJON<SoftwareBitBang> bus;
```
Now the PJON bus runs with the default strategy (see [Strategies](https://github.com/gioblu/PJON/wiki/Strategies)) [SoftwareBitBang](https://github.com/gioblu/PJON/wiki/SoftwareBitBang), and any id is assigned to the device, but it can acquire an id using the `acquire_id` method or communicate using `BROADCAST` id. If you need to predefine the device's id on instantiation, pass the selected id as shown below:
```cpp  
  PJON<> bus(1); // Device id 1
```
If you need to communicate on a noisy medium like radio, laser or infrared light, [OverSampling](https://github.com/gioblu/PJON/wiki/OverSampling) strategy can be used effectively (i.e. using cheap 315/433Mhz radio transcerivers maximum range is up to 5km with line of sight):
```cpp  
  PJON<OverSampling> bus;
```
Using this particular physical layer, synchronous acknowledge can reduce the maximum range, on certain media, so if you detect reduced range performance in `HALF_DUPLEX` compared to a mono-directional or `SIMPLEX` communication, and you can do without `ACK`, configure the absence of it after the packet transmission:
```cpp  
  bus.set_acknowledge(false);
```
 