**Media:** Radio, Wire |
**Pins used:** 2

`Oversampling` strategy comes from the PJON_ASK repository, and it was integrated in the PJON repository from version 3.0 beta, as a physical layer strategy. This strategy is inspired by SoftwareBitBang, but bits are over-sampled to have high resilience in high interference media like ASK/FSK cheap radio transceivers in urban environment. It is tested effectively with many versions of the ASK/FSK 433Mhz modules present on the market. This implementation works with a simple over-sampling approach, for this reason works also on wires and other radio modules.

####Compatibility
- Arduino Diecimila / Duemilanove
- Arduino Mini
- Arduino Uno
- Arduino Nano
- Arduino Mega

####Performance
- Transfer speed: 202 B/s or 1620 Baud
- Data throughput: 150 B/s
- Range: 250 meters in urban environment / 3km with LOS transmitting from a flying balloon

####Why not VirtualWire / RadioHead?
I don't think those libraries are clear, efficient and understandable enough to be the standard library for wireless radio communication available to the community, because of its implementation mess and complexity. Moreover VirtualWire doesn't have the support for multiple devices in multimaster setup, CRC, acknowledge, collision avoidance and packet management. For this reason I wrote this implementation to provide the user with the PJON standard also on wireless. :)