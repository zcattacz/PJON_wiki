![PJON Oversampling wiki](http://www.gioblu.com/PJON/PJON-radio.jpg)

**Media:** Radio, Wire |
**Pins used:** 2

`Oversampling` strategy comes from the [PJON_ASK](https://github.com/gioblu/PJON_ASK) repository, and it was integrated in the PJON repository from version 3.0 beta, as a physical layer strategy. This strategy is inspired by SoftwareBitBang, but bits are over-sampled to have high resilience in high interference media like ASK/FSK cheap radio transceivers in urban environment. It is tested effectively with many versions of the ASK/FSK 433Mhz modules present on the market. This implementation works with a simple over-sampling approach, for this reason works also on wires and other radio modules.

####Compatibility
- ATmega88/168/328 16Mhz (Diecimila, Duemilanove, Uno, Nano, Mini, Lillypad)
- ATmega2560 16Mhz (Arduino Mega)
- ATmega16u4/32u4 16Mhz (Arduino Leonardo)

####Performance
- Transfer speed: 202 B/s or 1620 Baud
- Data throughput: 150 B/s
- Range: 250 meters in urban environment / 3km with LOS transmitting from a flying balloon

####How to use SoftwareBitBang
Pass the `OverSampling` type as PJON template parameter to instantiate a PJON object ready to communicate in this Strategy. All the other necessary information is present in the general [Documentation](https://github.com/gioblu/PJON/wiki/Documentation).
```cpp  
  PJON<OverSampling> bus; // 2 pin over-sampled physical layer
```

####Why not VirtualWire / RadioHead?
I don't think those libraries are clear, efficient and understandable enough to be the standard library for wireless radio communication available to the community, because of its implementation mess and complexity. Moreover VirtualWire doesn't have the support for multiple devices in multimaster setup, CRC, acknowledge, collision avoidance and packet management. For this reason I wrote this implementation to provide the user with the PJON standard also on wireless. :)