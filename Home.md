Here you can find all the PJON standard definitions:
```cpp  
 ______________________________________________________________________________
| BIT_WIDTH 20 | BIT_SPACER 60 | ACCEPTANCE  8 | READ_DELAY 6    (microseconds)|
|------------------------------------------------------------------------------|
| Transfer speed: 4.32 kB/s    | Data throughput:  2.65 kB/s                   |
| Baud rate:      50000 baud/s |                                               |
| Accuracy:       99.995%      |                                               |
|______________________________________________________________________________| 

```
### Basic concepts
* Every device has an unique 1 byte ID (255 selectable ids)
* Every device transmit and receive on the same common PJON network medium
* Every device can be connected to n PJON network media (with n dedicated pins)
* Every device has equal right to transmit and receive on the common medium
* Device transmission occur only if communication medium is not in use
* Device syncronization happens every byte
* Devices communicate through packets

###Byte layer
Every byte is prepended with 2 syncronization padding bits. The first is a 60 microseconds logic 1 followed by a standard 20 microseconds logic 0. The reception tecnique is based on finding a logic 1 long as the first padding bit with a certain acceptance, syncronising to its falling edge and checking if it is followed by a standard logic 0. If this pattern is recognised byte reception starts, if not, interference, syncronization loss or simply absence of communication is detected at byte level. This adds a certain overhead to information but reduces the need of precise time tuning because syncronization is renewed every byte.
```cpp  
 __________ ___________________________
| Sync Pad | Byte                      |
|______    |___       ___     _____    |
|      |   |   |     |   |   |     |   |
|  1   | 0 | 1 | 0 0 | 1 | 0 | 1 1 | 0 |
|______|___|___|_____|___|___|_____|___|

```
All the first padding bit duration (60 microseconds) minus `ACCEPTANCE` (8 microseconds) is the syncronization window the receiver has for every incoming byte. Also `ACCEPTANCE` has a role in noise reduction, in fact filters out short logic 1 spikes as possible signs of interference.

###Packet layer
The concept of packet enables to send a communication payload to every connected device with correct reception certainty. Here an example of packet sending to device id 12 the string "@":
```cpp  
 ID 12            LENGTH 4         CONTENT @        CRC 130
 ________________ ________________ ________________ __________________
|Sync |    Byte  |Sync |    Byte  |Sync |    Byte  |Sync |    Byte    |
|___  |     __   |___  |      _   |___  |  _       |___  |  _      _  |
|   | |    |  |  |   | |     | |  |   | | | |      |   | | | |    | | |
|1  |0|0000|11|0 |1  |0|00000|1|00|1  |0|0|1|000000|1  |0|0|1|0000|1|0|
|___|_|____|__|__|___|_|_____|_|__|___|_|_|_|______|___|_|_|_|____|_|_|
```