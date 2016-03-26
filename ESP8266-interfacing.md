If you are creating a 3.3v only PJON bus the needed wiring is really simple:
![Wiring Diagram](http://www.gioblu.com/PJON/PJON_esp8266_esp8266.jpg)
If you are connecting an ESP8266 to a 5v bus you need to add some complexity to the standard PJON wiring. Logic level converter usage is strongly suggested. You risk to fry ESP8266 pins and to have communication problems due to logic 1 treshold of 5v boards. Here you can find a fritzing project made by aperepel: http://fritzing.org/projects/pjon-with-uno-and-esp8266nodemcu

Here's the diagram from the project. Note that 10K pulldown resistors are required on both ends.
![Wiring Diagram](http://www.gioblu.com/PJON/PJON_uno_esp8266_with_logic_level_converter.jpg)