Also if PJON is designed to be a really stable, interference and error aware communication standard, noise can variate enormously because of the environment, the setup and the medium you are using as communication channel. PJON single bidirectional medium is not a balanced pair, for this reason long distances and interference sources can affect communication reliability and data throughput.

Pull from PJON's desired implementation repository the master and run the example `NetworkAnalysis` and `SpeedTest`. This two sketches are designed to execute a test and respond through the Serial monitor with a benchmark on communication channel performance and reliability:
* Absolute communication speed
* Practical bandwidth or channel throughput
* Number of packets sent in the test window
* How many errors detected with CRC
* How many times the receive function ended with no reception
* How many times the channel is found busy
* Accuracy ( packets sent / packets received with mistakes ratio)

###Communication inaccurate or absent
Slow communication speed, a lot of CRC detected mistakes and / or channel often busy. Here you can find a list of really common issues that can lead to this problem:
  * If necessary (wire medium for example) use common ground for every device.
  * Wrong pin configuration in code.
  * Physical wiring configuration.
  * Device ID configuration in code.
  * Some other task is occupying all the available loop time.
  * Uncorrect packet length passed to `send()` function
  * Forgot the `update()` or `receive()` function in loop ;)  

If you have not identified the problem or you are a more advanced user, porting a new device / architecture, here you can find a list of common causes of this problem:

***

####Range 
* **Long distance between devices.**
* Many failed receptions.
* Many CRC detected mistakes.

You are probably near the maximum distance range of your system, A small capacitor can be a good solution to filter 0s that should be 1s and / or you can higher the transmission power.

***

####Interference
* **Channel found busy many times.**
* Many CRC detected mistakes.
* Low or absent communication speed.

Device avoid to transmit over noise to ensure correct communication, for this reason when the communication medium is affected by noise, data throughput and communication reliability drops. If wire or conductive element, evaluate its conducting performance and consider to use a pull-down resistor around megaohms order (try various values and see results). If radio or light waves, use a better antenna / photodiode, filter noise with physical (ground plane / lens) or discrete component, for example a capacitor, and / or higher transmission power.

***

####Timing
* **Many failed receptions.**
* Many CRC detected mistakes.
* Low or absent communication speed.

Bad syncronization or timing configuration. If you are porting a new device or architecture try to change timings in `PJON.h` and consider that every architecture will generate slightly dirrent durations.

***

####Execution time
* **Low quality of communication also after tuning / timing tweek.**
* Many failed receptions.
* Many CRC detected mistakes.
* Low or absent communication speed.

A still not implemented architecture / device may not be fast enough to run PJON, try using a faster clock or optimize digital I/O (see `digitalWriteFast.h`)

***
