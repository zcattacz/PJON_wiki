If you are creating a 3.3v only PJON bus the needed wiring is really simple:

![Wiring Diagram](http://www.gioblu.com/PJON/PJON%20-%20ESP8266%20to%20ESP8266%20communication.png)

If you are connecting an ESP8266 to a 5v bus you need to add some complexity to the standard PJON wiring. Logic level converter usage is strongly suggested otherwise you risk to fry ESP8266 pins and to have communication problems due to logic 1 treshold of 5v boards. Here you can find a fritzing project made by aperepel: http://fritzing.org/projects/pjon-with-uno-and-esp8266nodemcu

Note: 
- Add 1-2KOhm pulldown resistors on Arduino Duemilanove / Uno / Nano side.
- Add 10KOhm pulldown resistors on ESP8266 side (converter without integrated pullup resistors). 
- Add 1-2KOhm  pulldown resistors on ESP8266 side (converter with integrated pullup resistors).

Here's the diagram from the project. 
![Wiring Diagram](http://www.gioblu.com/PJON/PJON%20-%20Uno%20ESP8266%20communication.png)