# Adding backlight control to cheap 5" HDMI screen
I have bought some time ago a 5" LCD with HDMI input and touch panel connected via usb (through GB32F103 (STM32F103 Chinese clone :smile: ) and XPT2046). I had some adventure with making this touch to work (this is on one of my github repos :smile: ). I hacked my LCD because I got inspired on official Raspberry Pi forum by topic https://www.raspberrypi.org/forums/viewtopic.php?f=44&t=126321 .
Well, actually I did this to show that this is possible and quiet simple (for me :evil: ) 

## Backlight control
I thought of few ways to control backlight, but actually only two are the best ways to do this:
1. Control via EN input the backlight step-up controller
2. Replace switch with small signal relay (G5V/G6K/G6J/N4100/etc.) and drive relay by transitor from GPIO

I'm going to focus on the 1st way, because I don't have a switch on my LCD and I think it's the most proffesional way of doing this - I don't like idea of cutting load (in LED diodes form) from dedicated switched DC/DC controller for powering LEDs.

## Step 1
Reverse engeenering circuit :) , fortunatly chip that power-up LEDs is marked well, and it was easy to find by google "4103 sot23-6" (sot23-6 - this is name of this chip chassis). Just fast look at datasheet while looking at pcb and - yes, this is this one :) . 
PT4103 have EN input, that driven low will disable chip functions, and driving EN high (more that 1,5V) will enable the chip operation.

![DC-DC converter](http://github.com/saper-2/lcd-5in--backlight-hack-v1/raw/master/lcd5in-hdmi-dcdc.jpg)

## Step 2
EN input is connected to 5V rail from power socket (microusb). That's mean chip is constantly enabled, I'm also lucky because there is already pull-up resistor 10k (R26 in my pcb version). So just solder a thin wire (for reference this one is called on aliexpress "wrapping wire" :smile: ) to the 10k resistor from EN pin side, power up LCD module and touch with wire to usb socket chassis (chassis is connected to GND/0V), backlight shoud turn off - if not then (in worst case) there would be a spark and wire may burn out (and backlight might also never turn on again :wink:)... But I'm confident of my knowledge and expirence :smile:

This is how the circuit is made on my LCD board (blue wires). The <font color="green">green part</font> is what I added. I used RK7002 N-MOSFET transistor with Vgs(th)=1,5V , and resistor have around 51kOhms - I had this one value in 0603 chassis, can be anything between 33k..47k..75k . :smile: .

![Schematic](http://github.com/saper-2/lcd-5in--backlight-hack-v1/raw/master/lcd5in-hdmi-bl-hack-sch.jpg)

I have cutted a small area on pcb from GND fill ,and used this to solder wire to GPIO, gate of transistor and resistor.

Some photos:
![Thin wire solder](http://github.com/saper-2/lcd-5in--backlight-hack-v1/raw/master/lcd5in-hdmi-1.jpg)
![Tools and cutted some space from pcb for transistor](http://github.com/saper-2/lcd-5in--backlight-hack-v1/raw/master/lcd5in-hdmi-2.jpg)
![Cut place for gate, R and GPIO wire](http://github.com/saper-2/lcd-5in--backlight-hack-v1/raw/master/lcd5in-hdmi-3.jpg)
![Soldered 1](http://github.com/saper-2/lcd-5in--backlight-hack-v1/raw/master/lcd5in-hdmi-4.jpg)
![Soldered 2](http://github.com/saper-2/lcd-5in--backlight-hack-v1/raw/master/lcd5in-hdmi-5.jpg)
![Soldered 3](http://github.com/saper-2/lcd-5in--backlight-hack-v1/raw/master/lcd5in-hdmi-6.jpg)

## Step 3 - Final

Final look:
![Soldered 3](http://github.com/saper-2/lcd-5in--backlight-hack-v1/raw/master/lcd5in-hdmi-final.jpg)

Movie on Youtube with testing how EN behaves: https://www.youtube.com/watch?v=TtfMw2QhIUY

## Step 4 - Software

I have installed wiringPi for this testing my hack because it have a command line tool that allow to control GPIO pins.

I use GPIO 21 of BCM numbering style. I have tested this on RPi2. You can use any free GPIO pin that fits you.

First setup the GPIO pin as output:
```
gpio -g mode 21 out
```

Now to switch on backlight set GPIO pin to low state - to 0V :
```
gpio -g write 21 0
```

TO turn off backlight set GPIO pin to high state - to 3,3V :
```
gpio -g write 21 1
```

And movie on Youtube with results of my hack :smile: https://www.youtube.com/watch?v=lP1gdCcOtek
