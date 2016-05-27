![PJON Oversampling wiki](http://www.gioblu.com/PJON/PJON-radio.jpg)

**Media:** Radio, Wire |
**Pins used:** 1 / 2

Oversampling strategy comes from the [PJON_ASK](https://github.com/gioblu/PJON_ASK) repository, and it was integrated in the PJON repository from version 3.0 beta, as a physical layer strategy. Bits are over-sampled to have high resilience in high interference scenarios like ASK/FSK cheap radio transceivers in urban environment. It is tested effectively with many versions of the ASK/FSK 315/433Mhz modules available on the market.

####Compatibility
- ATmega88/168/328 16Mhz (Diecimila, Duemilanove, Uno, Nano, Mini, Lillypad)
- ATmega2560 16Mhz (Arduino Mega)
- ATmega16u4/32u4 16Mhz (Arduino Leonardo)

####Performance
- Transfer speed: 202 B/s or 1620 Baud
- Data throughput: 150 B/s
- Range: 250 meters in urban environment / 5km with LOS transmitting from a flying balloon

####Why not VirtualWire / RadioHead?
I don't think those libraries are clear, efficient and understandable enough to be the standard library for wireless radio communication available to the community, because of its implementation mess and complexity. Moreover VirtualWire doesn't have the support for multiple devices in multimaster setup, CRC, acknowledge, collision avoidance and packet management. For this reason I wrote this implementation to provide the user with the PJON standard also on wireless. :)

####How to use OverSampling
Pass the `OverSampling` type as PJON template parameter to instantiate a PJON object ready to communicate in this Strategy. All the other necessary information is present in the general [Documentation](https://github.com/gioblu/PJON/wiki/Documentation).
```cpp  
  PJON<OverSampling> bus; // 2 pin over-sampled physical layer
```

####Use OverSampling with cheap 433Mhz transceivers
To build a real open-source PJON packet radio able to communicate up to 5km you need only a couple (for SIMPLEX mode) or two couples (for HALF_DUPLEX mode) of cheap 315/433Mhz ASK/FSK transmitter / receiver modules (the total cost should be around 2/3 dollars). Please be sure of the regulations your government impose on radio transmission over these frequencies before use.

![PJON Oversampling packet radio](http://www.gioblu.com/PJON/PJON-OverSampling-packet-radio2.jpg)

The first suggested antenna design is a wide beam pseudo half-wavelength dipole antenna made by two 345mm long conductive elements, one connected to ground and the other connected to the input or output pin:
```cpp  

      345mm                    345mm                  345mm                    345mm
 -------------------|--------------------        -------------------|--------------------
                  __|__                                          ___|________
                 | tx  |                                        | rx         |
                 |_____|                                        |____________|

```
A more directional and long range antenna design is the pseudo half wavelength wip antenna. Can be easily crafted with two 345mm long insulated wire sections wrapped with each other every 5mm, one is connected to ground and the other to the input or output pin.

Using `OverSampling` physical layer, synchronous acknowledge can reduce the maximum range, on certain media, so if you detect reduced range performance in `HALF_DUPLEX` compared to a mono-directional or `SIMPLEX` communication, and you can do without `ACK`, configure the absence of it after the packet transmission:
```cpp  
  bus.set_acknowledge(false);
```
