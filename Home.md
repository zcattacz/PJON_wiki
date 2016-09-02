![PJON wiki](http://www.gioblu.com/PJON/PJON-mars.jpg)
PJON (Padded Jittering Operative Network) is an Arduino compatible, multi-master, multi-media communications bus system. It proposes a Standard and it is designed as a framework to ease digital communication. Thanks to a physical layer transport agnostic approach the PJON protocol layer is able to communicate data regardless of the medium and the procedure used through the concept of [Strategies](https://github.com/gioblu/PJON/wiki/Strategies). Its more common applications are in the field of internet of things and embedded systems. Extended tests proved its effectiveness on different media like electricity, radio and light.

### Basic concepts
* Every bus has a unique IPv4 like 4 bytes id
* Many buses can coexist on the same medium
* Every device has a unique 1 byte id
* Every device has an equal right to transmit and receive on the common medium
* Every device can be connected to n PJON buses
* Transmission occurs only if the communication medium is not in use
* Devices communicate through packets
* Packet transmission is regulated by a 1 byte header

The PJON protocol layer handles internal bus connectivity and unique addressing for 254 devices, through bus communication with unique bus addressing for 4.294.967.295 buses, supporting up to 1.090.921.692.930 devices.

###Bus
A PJON bus is made by a collection of up to 255 devices transmitting and receiving on the same medium. Communication between devices occurs through packets and it is based on democracy: every device has the right to transmit on the common medium for up to `(1000 / devices number) milliseconds / second`.
```cpp  
    _______     _______     _______     _______     _______
   |       |   |       |   |       |   |       |   |       |  
   | ID 0  |   | ID 1  |   | ID 2  |   | ID 3  |   | ID 4  |  
   |_______|   |_______|   |_______|   |_______|   |_______|    
 ______|___________|___________|___________|___________|______
          ___|___     ___|___     ___|___     ___|___
         |       |   |       |   |       |   |       |   
         | ID 5  |   | ID 6  |   | ID 7  |   | ID 8  |
         |_______|   |_______|   |_______|   |_______|    
```


###Packet transmission
The concept of packet enables to send a communication payload to every connected device with correct reception certainty. A packet contains the recipient id, its length, its header, its content and the CRC. In this example is shown a packet transmission in a local bus to device id 12 containing the string @ (decimal 64):
```cpp  

 RECIPIENT ID 12  LENGTH 5          HEADER 1        CONTENT 64       CRC 72
 ________________ _________________ ________________ ________________ __________________
|Sync | Byte     |Sync | Byte      |Sync | Byte     |Sync | Byte     |Sync | Byte       |
|___  |     __   |___  |      _   _|___  |      _   |___  |  _       |___  |  _    _    |
|   | |    |  |  |   | |     | | | |   | |     | |  |   | | | |      |   | | | |  | |   |
| 1 |0|0000|11|00| 1 |0|00000|1|0|1| 1 |0|00000|1|00| 1 |0|0|1|000000| 1 |0|0|1|00|1|000|
|___|_|____|__|__|___|_|_____|_|_|_|___|_|_____|_|__|___|_|_|_|______|___|_|_|_|__|_|___|
```
A default local packet transmission is a bidirectional communication between two devices that can be divided in 3 different phases: **channel analysis**, **transmission** and **response**. The packet transmission procedure is regulated by its header.

###Header configuration
The header bitmask let the packet's receiver handle the exchange as transmitter requested.
```cpp
 _______________________________________ _______________________________________
| 00000110 | Acknowledge requested     | Sender info included     | Local bus  | DEFAULT
| 00000100 | Acknowledge requested     | Sender info not included | Local bus  |
| 00000010 | Acknowledge not requested | Sender info included     | Local bus  |
| 00000000 | Acknowledge not requested | Sender info not included | Local bus  |
|----------|-------------------------- |--------------------------|------------|
| 00000111 | Acknowledge requested     | Sender info included     | Shared bus |
| 00000101 | Acknowledge requested     | Sender info not included | Shared bus |
| 00000011 | Acknowledge not requested | Sender info included     | Shared bus |
| 00000001 | Acknowledge not requested | Sender info not included | Shared bus |
|__________|___________________________|__________________________|____________|
```
As you can see for now, only the uppermost bit states are used for packet transmission exchange configuration, the unused bits may be used in future to extend or optimize the PJON Standard, so is suggested not make use of them on application level.

```cpp  
Channel analysis   Transmission                                     Response
    _____           ________________________________________           _____
   | C-A |         | ID | LENGTH |  HEADER  | CONTENT | CRC |         | ACK |
<--|-----|---< >---|----|--------|----------|---------|-----|--> <----|-----|
   |  0  |         | 12 |   4    | 00000100 |    64   |  72 |         |  6  |
   |_____|         |____|________|__________|_________|_____|         |_____|
```
In the first phase the bus is analyzed by transmitter reading 10 logical bits, if any logical 1 is detected the channel is considered free, transmission phase starts in which the packet is entirely transmitted. Receiver calculates CRC and starts the response phase transmitting a single byte, `ACK` (decimal 6) in case of correct reception or `NAK` (decimal 21) if an error in the packet's content is detected. If transmitter receives no answer or `NAK` the packet sending is scheduled with a delay of `ATTEMPTS * ATTEMPTS * ATTEMPTS` with a maximum of 125 `ATTEMPTS` to obtain data transmission 3rd degree polynomial backoff. 

###Bus network
A PJON bus network is the result of n PJON buses sharing the same medium and or interconnection of PJON buses using routers. A router is a device connected to n PJON buses with n dedicated pins on n dedicated media, able to route a packet from a bus to anotherone.

```cpp  
   TWO BUSES CONNECTED THROUGH A ROUTER
    _______     _______              _______     _______
   |       |   |       |            |       |   |       |
   | ID 0  |   | ID 1  |            | ID 0  |   | ID 1  |
   |_______|   |_______|  ________  |_______|   |_______|
 ______|___________|_____| ROUTER |_____|___________|______
          ___|___        |  ID 3  |        ___|___
         |       |       |________|       |       |
         | ID 2  |                        | ID 2  |
         |_______|                        |_______|
```

In a shared medium (like 433Mhz channel-less transceivers) it is necessary to define a bus id to isolate devices from outcoming communication of other buses nearby, enabling many to coexist on the same communication medium. 
```cpp  
   TWO BUSES SHARING THE SAME MEDIUM
     
       BUS ID 0.0.0.1                 BUS ID 0.0.0.2
    _______     _______              _______     _______
   |       |   |       |            |       |   |       |
   | ID 0  |   | ID 1  |            | ID 0  |   | ID 1  |
   |_______|   |_______|            |_______|   |_______|
 ______|___________|___________________|___________|______
          ___|___                          ___|___
         |       |                        |       |
         | ID 2  |                        | ID 2  |
         |_______|                        |_______|

```
Below is shown the same local transmission used as an example before, formatted to be sent in a shared environment, where device id `12` of bus `0.0.0.1` sends @ (decimal 64) to device id `11` in bus id `0.0.0.1`. The packet's content is prepended with the bus id of the recipient, and optionally the sender's bus and device id:
```cpp  
Channel analysis                     Transmission                              Response
 _____     _________________________________________________________________     _____
| C-A |   | ID | LENGTH | HEADER |   BUS ID   | BUS ID | ID | CONTENT | CRC |   | ACK |
|-----|< >|----|--------|--------|------------|--------|----|---------|-----|> <|-----|
|  0  |   | 12 |   15   |  111   |    0001    |  0001  | 11 |   64    |     |   |  6  | 
|_____|   |____|________|________|____________|________|____|_________|_____|   |_____|
                                 |  RX INFO   |   TX INFO   |
```
Thanks to this rule is not only possible to share a medium with neighbours, but also network with them and enhance connectivity for free.

###License

```cpp
/* Copyright 2012-2016 Giovanni Blu Mitolo

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License. */
```