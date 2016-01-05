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

####Communication inaccurate or absent
Really slow communication speed, with a lot of CRC detected mistakes and / or channel often busy.

* Really common issues:
  * Wrong pin configuration in code.
  * Physical wiring configuration.
  * Device ID configuration in code.
  * Some other task is occupying all the available loop time.
  * If necessary (for example with wired implementation) use common ground for every device.
  * Forgot the `update()` or `receive()` function in loop ;)? 

* For more advanced users:
  * **Range** (You are probably near the maximum distance range of your system).
    * A small capacitor can be a good solution to filter 0s that should be 1s. 
  * **Timing** (Bad syncronization or timing configuration).
    * If you are porting a new device or architecture try to change timings in `PJON.h`.
    * Consider that every architecture will generate slightly dirrent durations.
  * **Execution time** (a new architecture / device may not be fast enough to run PJON).
    * Use faster clock
    * Optimize digital I/O (see `digitalWriteFast.h`)
  * **Interference** is generating noise. (Device avoid to transmit over noise)
    * If wire or conductive element, evaluate its conducting performance and consider to use a pull-down resistor around megaohms order (try various values and see results).
    * If radio or light waves, use a better antenna / photodiode, filter noise with physical (ground plane / lens) or discrete component, for example a capacitor, and / or higher transmission power.