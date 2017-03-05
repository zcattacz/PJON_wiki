# Add Configuration

Before approaching to the PJON class it is possible to define the packets and content buffer length.  Pre-defining `MAX_PACKETS` and `PACKET_MAX_LENGTH` it is possible to configure this constants to reach the project and memory requirements. Obviously, the less memory is dedicated to this buffers, the more memory can be used for something else.
```cpp  
#define MAX_PACKETS 1
#define PACKET_MAX_LENGTH 20
#include <PJON.h>
/* PJON can store up to 1 packet of up to 
   20 characters - packet overhead (from 4 to 13 depending by configuration) */
```

Templates can be scary at first sight, but they are quite straight-forward and efficient. Lets start coding, looking how to instantiate in the simplest way the `PJON` object that in the example is called bus with a wire compatible physical layer:
```cpp  
  PJON<> bus;

  // or 

  PJON<SoftwareBitBang> bus;
```
The PJON bus runs by default through the [SoftwareBitBang](SoftwareBitBang) strategy.

If you need to predefine the device's id on instantiation, pass the selected id as shown below:
```cpp  
  PJON<> bus(1); // Device id 1
```
If you need to communicate on a noisy medium like radio, laser or infrared light, [OverSampling](OverSampling) strategy can be used effectively (i.e. using cheap 315/433Mhz radio transcerivers maximum range is up to 5km with line of sight):
```cpp  
  PJON<OverSampling> bus;
```
Configure network state (local or shared). If local, so if passing `false`, the PJON protol layer procedure is based on a single byte device id to univocally communicate with a device; if in shared mode, so passing `true`, the protocol adopts a 4 byte bus id to univocally communicate with a device in a certain bus:
```cpp  
  bus.set_shared_network(true);
```
If you are transmitting on a shared medium it is better to define a bus id to isolate your networking from other buses nearby and to avoid unwanted and potentially critical collisions (i.e. opening the neighbour's garage door, when you just wanted to brighten your kitchen):
```cpp  
  // Bus id definition
  uint8_t bus_id[] = {0, 0, 0, 1};

  // PJON object
  PJON<OverSampling> bus(bus_id, 45);
```
In this example we are defining a new bus id made by 4 bytes, and we are passing it to the PJON object followed by the chosen and predefined device id.

Configure the communication mode:
```cpp  
  bus.set_communication_mode(SIMPLEX);     // Run in mono-directional SIMPLEX mode
  bus.set_communication_mode(HALF_DUPLEX); // Run in bi-directional HALF_DUPLEX mode
```
Configure acknowledge:
```cpp  
  bus.set_acknowledge(false);
```
PJON by default includes the sender information in the packet. If you don't need this information you can use the provided setter to reduce overhead and higher communication speed:
```cpp  
  bus.include_sender_info(false);
```
Configure your device to act as a router, so receiving all the incoming packets:
```cpp  
  bus.set_router(true);
```
Avoid packet auto-deletion:
```cpp  
  bus.set_packet_auto_deletion(false);
```
