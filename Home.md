PJON is an opensource multimaster communications bus system Standard created to provide the community with a new and easy way to communicate data and build networks of devices. Its more common applications are in the field of internet of things and embedded systems. Extended tests proved its effectiveness on different media like electricity, radio frequency and light.

### Basic concepts
* Every device has an unique 1 byte ID (0-255)
* Every device transmits and receives on the same common PJON network medium
* Every device has an equal right to transmit and receive on the common medium
* Every device can be connected to n PJON network media (with n dedicated pins)
* Transmission occurs only if the communication medium is not in use
* Synchronization occurs every byte
* Devices communicate through packets

***

###Byte transmission
Every byte is prepended with 2 synchronization padding bits and transmission occurs LSB-first (although in the following graphs is presented for clarity as MSB-first). The first is a longer than standard logic 1 followed by a standard logic 0. The reception tecnique is based on finding a logic 1 as long as the first padding bit within a certain threshold, synchronizing to its falling edge and checking if it is followed by a logic 0. If this pattern is recognised, reception starts, if not, interference, synchronization loss or simply absence of communication is detected at byte level.
```cpp  
 __________ ___________________________
| SyncPad  | Byte                      |
|______    |___       ___     _____    |
|  |   |   |   |     |   |   |     |   |
|  | 1 | 0 | 1 | 0 0 | 1 | 0 | 1 1 | 0 |
|_ |___|___|___|_____|___|___|_____|___|
   |
 ACCEPTANCE
```
This adds a certain overhead to information but reduces the need of precise time tuning because synchronization is renewed every byte. All the first padding bit duration minus `ACCEPTANCE` is the synchronization window the receiver has for every incoming byte. If the length of the first padding bit is less than `ACCEPTANCE` the received signal is considered interference.

***

###Packet transmission
The concept of packet enables to send a communication payload to every connected device with correct reception certainty. A packet contains the recipient id, the length of the packet, its content and the CRC. Here is an example of a packet sending to device id 12 containing the string "@":
```cpp  
 ID 12            LENGTH 4         CONTENT 64       CRC 130
 ________________ ________________ ________________ __________________
|Sync | Byte     |Sync | Byte     |Sync | Byte     |Sync | Byte       |
|___  |     __   |___  |      _   |___  |  _       |___  |  _      _  |
|   | |    |  |  |   | |     | |  |   | | | |      |   | | | |    | | |
| 1 |0|0000|11|00| 1 |0|00000|1|00| 1 |0|0|1|000000| 1 |0|0|1|0000|1|0|
|___|_|____|__|__|___|_|_____|_|__|___|_|_|_|______|___|_|_|_|____|_|_|
```
A standard packet transmission is a bidirectional communication between two devices that can be divided in 3 different phases: **channel analysis**, **transmission** and **response**. 
```cpp  
  Channel analysis   Transmission                            Response
      _____           _____________________________           _____
     | C-A |         | ID | LENGTH | CONTENT | CRC |         | ACK |
  <--|-----|---------|----|--------|---------|-----|--> <----|-----|
     |  0  |         | 12 |   4    |   64    | 130 |         |  6  |
     |_____|         |____|________|_________|_____|         |_____|
```
In the first phase the bus is analyzed by transmitter reading 10 logical bits, if no logical 1s are detected the channel is considered free, transmission phase starts in which the packet is entirely transmitted. Receiver calculates CRC and starts the response phase transmitting a single byte, `ACK` (dec 6) in case of correct reception or `NAK` (dec 21) if an error in the packet's content is detected. If transmitter receives no answer or `NAK` the packet sending has to be scheduled with a delay of `ATTEMPTS * ATTEMPTS` with a maximum of 250 `ATTEMPTS` to obtain data transmission quadratic backoff. 

***

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

***

###Bus network
A PJON bus network is the result of interconnecting n PJON bus using routers. A router is a device connected to n PJON bus (with n dedicated pins) able to route a packet from a bus to anotherone. All the routing procedure is managed by an higher level protocol called [OSPREY](https://github.com/gioblu/OSPREY) still far from completion where I am actually brainstorming how the routing and networking Standard will be structured.

```cpp  
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

***

###License

```cpp
/* Copyright (c) 2012-2016, Giovanni Blu Mitolo All rights reserved.

-  All advertising materials or physical products mentioning features or 
   use of this Standard must display the following acknowledgement:
    "Powered by the PJON standard"

-  Neither the name of PJON, PJON_ASK nor the
   names of its contributors may be used to endorse or promote products
   derived from the PJON Standard without specific prior written permission.

This Standard is provided by the copyright holders and contributors "as is" 
and any express or implied warranties, including, but not limited to, the 
implied warranties of merchantability and fitness for a particular purpose 
are disclaimed. In no event shall the copyright holder or contributors be 
liable for any direct, indirect, incidental, special, exemplary, or consequential 
damages (including, but not limited to, procurement of substitute goods or services; 
loss of use, data, or profits; or business interruption) however caused and on any 
theory of liability, whether in contract, strict liability, or tort (including 
negligence or otherwise) arising in any way out of the use of this software, even if 
advised of the possibility of such damage. */
```