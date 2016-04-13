Also if PJON is designed to be a really stable, interference and error aware communication Standard, noise can variate enormously because of the environment, the setup and medium you are using as communication channel. PJON single bidirectional medium is not a balanced pair, for this reason long distances and interference sources can affect communication reliability and data throughput.

If you have a Saleae Logic Analyzer you will be amazed by [saleae-pjon-protocol-analyzer](https://github.com/aperepel/saleae-pjon-protocol-analyzer) crafted by the user aperepel, it offer a complete analysis suite aware of the Standard functional procedure and symbols.

Pull from PJON's desired implementation repository the master and run the example `NetworkAnalysis`, `SpeedTest` and `ErrorTest`. These 3 sketches are designed to execute a test and respond through the Serial monitor with a benchmark on communication channel performance and reliability:
* Absolute communication speed
* Practical bandwidth or channel throughput
* Number of packets sent in the test window
* How many errors detected with CRC
* How many times the receive function ended with no reception
* How many times the channel is found busy
* Accuracy ( packets sent / packets received with mistakes ratio)

###Communication inaccurate or absent
Absent or slow communication speed, a lot of CRC detected mistakes and / or channel often busy. 
Here you can find a list of really common issues that can lead to this problem:
  * If necessary (i.e. wire / conductive medium) use common ground for every device.
  * Pin configuration in your code.
  * Physical wiring to the pin.
  * Device ID configuration in your code.
  * Other tasks are occupying all the available loop time.
  * Uncorrect packet length passed to `send()` function
  * Forgot the `update()` or `receive()` function in loop ;)  

If you haven't identified the problem or you are a more advanced user, porting a new device / architecture, here you can find a list of common causes of this problem:

####Range 
* **Long distance between devices.**
* Many failed receptions.
* Many mistakes detected by CRC.

You are probably near the maximum distance range of your system. The most straight-forward solution is to higher transmission power or to use a small capacitor to filter 1s received as 0s because of distance weakened voltage.

####Interference
* **Channel detected busy many times.**
* Many mistakes detected by CRC.
* Low or absent communication speed.

Device avoids to transmit over noise to ensure correct communication, when the communication medium is affected by noise, data throughput and communication reliability drops. Because of interference are also detected mistakes by CRC. If using a wire or a conductive element, evaluate its conducting performance and consider to use a pull-down resistor around megaohms order (try various and see results). 

####Timing
* **Many failed receptions.**
* Many mistakes detected by CRC.
* Low or absent communication speed.

Bad syncronization or timing configuration. If you are porting a new device or architecture try to tweak `BIT_WIDTH`, `BIT_SPACER`, `READ_DELAY` and `ACCEPTANCE` in `PJON.h` and consider that every architecture will execute code with a different timing.

####Execution time
* **Low quality of communication also after tuning / timing tweek.**
* Many failed receptions.
* Many CRC detected mistakes.
* Low or absent communication speed.

Every architecture needs a different time to execute the same PJON's code, so at the point where it should start to read the first bit of a byte, after initial padding bits, it can be a little shifted in time relatively to the transmitter, and so not able to read correctly the bit sequence. `READ_DELAY` constant in `PJON.h` regulates the correct positioning in every bit of the 8 readings in time. Take in consideration that a still not implemented architecture / device may not be fast enough to run PJON, try using a faster clock or optimize digital I/O (see `digitalWriteFast.h`).