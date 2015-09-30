PJON is an opensource multimaster communications bus system standard. It is created to provide the community with a new and easy way to communicate data and build networks of devices. Its more common applications are in the field of internet of things and embedded systems. Extended tests proved its effectiveness on different media like electricity, radio frequency and light. A Bit has a duration of 20 microseconds and communication speed is 4.16kB/s or 50000 baud. This setup is selected to have an acceptable bandwidth but also to be easily implemented bitbanged or software emulated on low frequency clock microcontrollers.
### Basic concepts
* Every device has an unique 1 byte ID (0-255)
* Every device transmits and receives on the same common PJON network medium
* Every device has an equal right to transmit and receive on the common medium
* Every device can be connected to n PJON network media (with n dedicated pins)
* Transmission occurs only if the communication medium is not in use
* Synchronization occurs every byte
* Devices communicate through packets

###Byte transmission
Every byte is prepended with 2 synchronization padding bits. 
The first is a 60 microseconds logic 1 followed by a standard 20 microseconds logic 0. The reception tecnique is based on finding a logic 1 as long as the first padding bit within a certain threshold, synchronizing to its falling edge and checking if it is followed by a 20 microseconds logic 0. If this pattern is recognised, reception starts, if not, interference, synchronization loss or simply absence of communication is detected at byte level.
```cpp  
 __________ ___________________________
| Sync Pad | Byte                      |
|______    |___       ___     _____    |
|      |   |   |     |   |   |     |   |
|  1   | 0 | 1 | 0 0 | 1 | 0 | 1 1 | 0 |
|______|___|___|_____|___|___|_____|___|

```
This adds a certain overhead to information but reduces the need of precise time tuning because synchronization is renewed every byte. All the first padding bit duration (60 microseconds) minus `ACCEPTANCE` (8 microseconds) is the synchronization window the receiver has for every incoming byte. If the length of the first padding bit is less than `ACCEPTANCE` is considered interference.

###Packet transmission
The concept of packet enables to send a communication payload to every connected device with correct reception certainty. A packet contains the recipient id, the length of the packet, its content and the CRC. Here is an example of a packet sending to device id 12 containing the string "@":
```cpp  
 ID 12            LENGTH 4         CONTENT 64       CRC 130
 ________________ ________________ ________________ __________________
|Sync | Byte     |Sync | Byte     |Sync | Byte     |Sync | Byte       |
|___  |     __   |___  |      _   |___  |  _       |___  |  _      _  |
|   | |    |  |  |   | |     | |  |   | | | |      |   | | | |    | | |
| 1 |0|0000|11|0 | 1 |0|00000|1|00| 1 |0|0|1|000000| 1 |0|0|1|0000|1|0|
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
In the first phase the bus is analyzed by transmitter reading 10 logical bits, if no logical 1s are detected the channel is considered free, transmission phase starts and the packet is entirely transmitted. Receiver calculates CRC and starts the response phase transmitting a single byte, `ACK` (dec 6) in case of correct reception or `NAK` (dec 21) if an error in the packet's content is detected. If transmitter receives no answer or `NAK` the packet sending has to be scheduled with a delay of `ATTEMPTS * ATTEMPTS` with a maximum of 250 `ATTEMPTS` to obtain data transmission exponential backoff. 

###Network
A PJON network is made by a collection of up to 255 devices transmitting and receiving on the same medium. Communication between devices occurs through packets and it is based on democracy: every device has the right to transmit on the common medium for up to `(1000 / devices number) milliseconds / second`.   

(work in progress)