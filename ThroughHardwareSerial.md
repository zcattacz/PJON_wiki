
With ThroughHardwareSerial PJON can run through the Serial port of your device. Both ports should be free from USB computer connection and data pins should be wired inverted TX->RX, RX->TX.

####Why to add PJON over Serial?
Serial communication is an hardware integrated communication medium that can reach very fast communication speed but it is quite basic and lacks many of the necessary structures and entities for an easy setup of a network communication.

####Serial Downsides:
- No CRC, checksum or parity bit (ensure correct data transfert)
- No transmission certainty (lack of acknowledge from receiver)
- No packet handling (no retrasmission in case of error)
- No idea of id (no chance to connect more than 2 devices together) 

####Adding PJON over Serial you get:
- Correct transmission certainty with the use of CRC 8-bit
- Acnowledge byte to inform transmitter of correct data reception
- Packet handling, i.e. example schedule a repeated packet transmission.
- Connect 2 slaves with a 1 master having univocal communication with device id ThroughHardwareSerial can run a 2 device bus in multi-master or a n devices bus in a master-slave setup. (n still unkown, tested with 1 master and 2 slaves)