Also if PJON is designed to be a really stable, interference and error aware communication standard, noise can variate enormously because of the environment, the setup and the medium you are using as communication channel. PJON single bidirectional medium is not a balanced pair, for this reason long distances and interference sources can affect the communication reliability and the practical bandwidth available.

####Test the actual performance
Pull the actual master from PJON's desired implementation repository and run the example NetworkAnalysis. This two sketches are designed to execute a test and respond through the serial monitor with various information on communication channel performance and reliability:
* Absolute communication speed
* Practical bandwidth or channel throughput
* Number of packets sent in the test window
* How many errors detected with CRC
* How many times the receive function ended with no reception
* How many times the channel is found busy
* Accuracy ( packets sent / packets received with mistakes ratio)

####Common problems
List of common problems you can face, to speed up the PJON implementaiton:

* The most common result is for sure to get an Absolute communication speed of 0:
  * Wrong pin configuration in your code
  * Physical wiring configuration
  * Device ID configuration.

* Really slow communication speed, with a lot of CRC detected mistakes or channel often busy:
  * Range (You are probably near the maximum distance range of your system).
    * A small capacitor can be a good solution to filter 0s that should be 1s. 
  * Timing (Bad syncronization or timing configuration).
    * If you are porting a new device or architecture try to change timings in PJON.h.
  * Execution time (a new architecture / device may not be fast enough to run PJON).
    * Use faster clock
    * Optimize digital i/o (see digitalWriteFast)
  * Interference is generating noise. (Devices avoid to transmit over noise to ensure reliability)
    * If wire or conductive element, use pull-down resistor around megaohms order (try various values and see results).
    * If radio / light waves, filter noise with physical / discrete component (for example a capacitor) or / and higher transmission power.