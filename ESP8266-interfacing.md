I was able to achieve 1130B/s effective transfer rate between UNO and ESP8266 with ~20-25% failed packets (all accounted for PJON). Raw speed around 1350B/s.

I used the logic level converter as UNO speaks 5v while ESP8266 is at 3.3v (we don't want UNO to fry any pins on ESP8266). Here's the actual converter used (it's generic, and I'm not endorsing any seller, just providing info on what it looks like): http://www.aliexpress.com/item/10PCS-IIC-I2C-Logic-Level-Converter-Bi-Directional-Module-5V-to-3-3V/32278273532.html

I've created a Fritzing project here as well: http://fritzing.org/projects/pjon-with-uno-and-esp8266nodemcu

Here's the diagram from the project. Note that 10K pulldown resistors are required on both ends.

![Wiring Diagram](http://www.gioblu.com/PJON/PJON_uno_esp8266_with_logic_level_converter.jpg)