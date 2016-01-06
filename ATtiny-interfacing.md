AVR ATtiny microcontroller family is a really interesting and compact platform, for this reason I worked to support it in the PJON [Arduino compatible implementation](https://github.com/gioblu/PJON) and it will be soon compatible also with [PJON_ASK wireless implementation](https://github.com/gioblu/PJON_ASK). 

####How to program ATtiny 45/85
You physically need at least one ATtiny microcontroller, a breadboard, some jumpers and an Arduino duemilanove / Uno used as an Arduino ISP programmer. Follow [High-Low Tech tutorial](http://highlowtech.org/?p=1706) by David Mellis and get the last version of the [attiny repository](https://github.com/damellis/attiny).

####Use PJON with ATtiny45/85
PJON [Arduino compatible implementation](https://github.com/gioblu/PJON) works smoothly on PIN 2 and 3, using PJON on PIN 1 and 4 leads to low performance or malfunction. The reasons why this happens are still to be clarified with study and experimentation.

