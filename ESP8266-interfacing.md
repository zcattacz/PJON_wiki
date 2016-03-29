If you are creating a 3.3v only PJON bus the needed wiring is really simple:

![Wiring Diagram](http://www.gioblu.com/PJON/PJON%20-%20ESP8266%20to%20ESP8266%20communication.png)

If you are connecting an ESP8266 to a 5v bus you need to add some complexity to the standard PJON wiring. Logic level converter usage is strongly suggested otherwise you risk to fry ESP8266 pins and to have communication problems due to logic 1 treshold of 5v boards. Here you can find a fritzing project made by aperepel: http://fritzing.org/projects/pjon-with-uno-and-esp8266nodemcu

Note: 
- 10K pulldown resistors are required on both ends with logic level converter wit no integrated pullup resistor. 
- 1K  pulldown resistors are required on both ends if using a logic level converter with integrated pullup resistor.

Here's the diagram from the project. 
![Wiring Diagram](http://www.gioblu.com/PJON/PJON%20-%20Uno%20ESP8266%20communication.png)