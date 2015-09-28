##PJON communication standard wiki
Here you can find all the PJON standard definitions.
```cpp  
 ______________________________________________________________________________
| BIT_WIDTH 20 | BIT_SPACER 60 | ACCEPTANCE  8 | READ_DELAY 6    (microseconds)|
|------------------------------------------------------------------------------|
| Transfer speed: 4.32 kB/s    | Data throughput:  2.65 kB/s                   |
| Baud rate:      50000 baud/s |                                               |
| Accuracy:       99.995%      |                                               |
|______________________________________________________________________________| 

```
## Basic concepts
* Every device has an unique 1 byte ID (255 selectable ids)
* Every device transmit and receive on the same common PJON network medium
* Every device can be connected to n PJON network media (with n dedicated pins)
* Every device has equal right to transmit and receive on the common medium
* Device transmission occur only if communication medium is not in use
* Device syncronization happens every byte
* Devices communicate through packets

###Byte layer
Every byte is prepended with 2 syncronization padding bits. The first padding bit is a 60 microseconds logic 1 bit followed by a standard 20 microseconds logic 0 bit. The reception tecnique is based on finding a logic 1 bit long as the first padding bit (with a certain acceptance) and syncronising to the falling edge of this bit, checking if it is followed by a standard logic 0 bit. If this pattern is found byte reception starts, if not, interference, syncronization loss or simply absence of communication is detected at byte level. This choice add a certain overhead to information but reduces the need of precise time tuning, so ease implementation, and gives a stable communication link.
```cpp  
 __________ ___________________________
| Sync Pad | Byte                      |
|______    |___       ___     _____    |
|      |   |   |     |   |   |     |   |
|  1   | 0 | 1 | 0 0 | 1 | 0 | 1 1 | 0 |
|______|___|___|_____|___|___|_____|___|

```
###Packet layer
The concept of packet enables to send a communication payload to every connected device with correct reception certainty. Here an example of packet sending to device id 12 the string "HI":
```cpp  
 ____________________________________ 
| ID | LENGTH | byte0 | byte1  | CRC |
|----|--------|-------|--------|-----|
| 12 |   5    |   H   |   I    | 134 |
|____|________|_______|________|_____|

```


