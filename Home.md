Here you can find all the PJON standard definitions:
```cpp  
 ____________________________________________________________________________
| BIT_WIDTH 20 | BIT_SPACER 60 | ACCEPTANCE  8 | READ_DELAY 6  (microseconds)|
|----------------------------------------------------------------------------|
| Transfer speed: 4.32 kB/s    | Data throughput:  2.65 kB/s                 |
| Baud rate:      50000 baud   |                                             |
| Accuracy:       99.995%      |                                             |
|____________________________________________________________________________| 

```
### Basic concepts
* Every device has an unique 1 byte ID (255 selectable ids)
* Every device transmits and receives on the same common PJON network medium
* Every device can be connected to n PJON network media (with n dedicated pins)
* Every device has an equal right to transmit and receive on the common medium
* Transmission occurs only if the communication medium is not in use
* Synchronization occurs every byte
* Devices communicate through packets

###Byte transmission
Every byte is prepended with 2 synchronization padding bits. The first is a 60 microseconds logic 1 followed by a standard 20 microseconds logic 0. The reception tecnique is based on finding a logic 1 as long as the first padding bit within a certain threshold, synchronizing to its falling edge and checking if it is followed by a standard length logic 0. If this pattern is recognised byte reception starts, if not, interference, synchronization loss or simply absence of communication is detected at byte level.
```cpp  
 __________ ___________________________
| Sync Pad | Byte                      |
|______    |___       ___     _____    |
|      |   |   |     |   |   |     |   |
|  1   | 0 | 1 | 0 0 | 1 | 0 | 1 1 | 0 |
|______|___|___|_____|___|___|_____|___|

```
This adds a certain overhead to information but reduces the need of precise time tuning because synchronization is renewed every byte. All the first padding bit duration (60 microseconds) minus `ACCEPTANCE` (8 microseconds) is the synchronization window the receiver has for every incoming byte. Also `ACCEPTANCE` has a role in noise reduction, in fact filters out short logic 1 as possible signs of interference.

###Packet transmission
The concept of packet enables to send a communication payload to every connected device with correct reception certainty. Here is an example of packet sending to device id 12 the string "@":
```cpp  
 ID 12             LENGTH 4          CONTENT 64        CRC 130
 ________________  ________________  ________________  __________________
|Sync | Byte     ||Sync | Byte     ||Sync | Byte     ||Sync | Byte       |
|___  |     __   ||___  |      _   ||___  |  _       ||___  |  _      _  |
|   | |    |  |  ||   | |     | |  ||   | | | |      ||   | | | |    | | |
| 1 |0|0000|11|0 || 1 |0|00000|1|00|| 1 |0|0|1|000000|| 1 |0|0|1|0000|1|0|
|___|_|____|__|__||___|_|_____|_|__||___|_|_|_|______||___|_|_|_|____|_|_|
```
A standard packet transmission is a bidirectional communication between two devices that can be divided in 3 different phases: **channel state analysis**, **transmission** and **response**. In the first phase the bus is analyzed by transmitter (reading 10 logical bits). If the 10 bits are all zero the channel is considered free and transmission phase starts.


(work in progress)