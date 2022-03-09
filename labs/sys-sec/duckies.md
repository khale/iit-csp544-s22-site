---
title: Dropped Drive Attacks
parent: System Security
nav_order: 3
summary: Social Engineering 
---

# Lab 10: Social Engineering and Rubber Duckies (Optional)

This lab will introduce you to dropped drive attacks, and using physical tools to manipulate systems. 

~~Due Date: Wednesday: 03/09/22 before class~~
**Due Date: Monday: 03/21/22 before class**

## Lab Overview
For this lab you will learn how to create a low-cost USB device that, when
plugged into a victim's system, can be used to gain system access, open up
backdoors, modify files, etc. This type device is sometimes generically called
a Rubber Ducky (named after [Hak5's device](https://shop.hak5.org/products/usb-rubber-ducky-deluxe)). The idea is that this device can be
used to infiltrate a system quickly and easily when physical access to the
target system is possible. Even if that's not the case, attackers can simply
leave the drives in a parking lot, and hope that someone picks it up and plugs
it into a target machine for them. These [dropped drive attacks](https://www.wired.com/2011/06/the-dropped-drive-hack/) are notoriously
effective, because [people actually pick them up](https://elie.net/publication/users-really-do-plug-in-usb-drives-they-find/).

## Lab Description
The basic idea of this hardware is that you will program it to act like a USB keyboard that automatically sends keystrokes to the target system when plugged in. The most common is to use a hot-key combination (cmd+space on Mac) to launch a terminal program and type in arbitrary commands (e.g. download a paylod from the attacker's server and execute it). A glaring weakness of this attack is that a user must be logged in and the system must be active for it to work.

### Task 1: Making it Blink
The board you have is a cheap Chinese knock-off of the Digispark development board, which itself is pretty similar to Hak4's official Rubber Ducky USB stick. Both are pretty expensive, and are out of stock anyway.

First download the latest [Arduino IDE](https://www.arduino.cc/en/software) package for your system, install, and run it.

Then you should go to the Preferences menu (`File -> Preferences`, or `Arduino -> Preferences` on Mac). In the box titled "Additional Boards Manager URLs" put in `http://digistump.com/package_digistump_index.json` and click OK.

We'll need to install the board drivers now. Go to `Tools -> Board -> Board Manager` and then from the drop-down menu select "Contributed". In the filter type in "Digistump" and when the package called "Digistump AVR Boards" comes up click to install it. This will take a few seconds.

Once that finishes, go to Tools -> Boards and select "Digispark" (the default option). Then go to Tools -> Programmer and select the "USBtinyISP" option. If this doesn't work for you, try to go to the [Digistump tutorial](http://digistump.com/wiki/digispark/tutorials/connecting).

Now we're ready to flash the device and try it out (but don't plug it in yet).
We'll start with a simple example Arduino script which just causes an LED on
the board to blink on and off. Luckily the board packages already include such
a script. Go to `File -> Examples -> Digispark_Examples ->` and select the
"Start" script. That should bring up some code. Take a look a code and see how
easy this API makes it to program the little board. To flash the device, (don't
insert it yet) hit the "Upload" button (it has a right arrow on it) and it will
then prompt you to plug in the board. Do so now. In a few seconds, you'll see
some messages print out in the Arduino console, hopefully ending with the line
"Micronucleus done. Thank you!". If so, your device is now programmed, and you
should see the red LED blinking at 1Hz. Let's make it do something more
interesting now.

**Note**: If you get an error like "bad CPU type" on a Mac you might have to
jump through another hoop to get the Arduino IDE to do the right thing. First
download a nightly build of the Arduino IDE instead, and then make sure to run
the following, where below I'm assuming that you're leaving the Arduino app in
`~/Downloads`.

```bash
cd ~/Library/Arduino15/packages/arduino/tools/avr-gcc
mv 4.8.1-arduino5/ orig.4.8.1
ln -s ~/Downloads/Arduino.app/Contents/Java/hardware/tools/avr 4.8.1-arduino5
```

### Task 2: Turning it into a Rubber Ducky
First make sure you have Java installed (check by opening a terminal and
running `java -version`). If you don't have it installed, you'll want to go [here](https://www.oracle.com/java/technologies/downloads/)
to get and install it.

Now we're going to download the Rubber Ducky toolchain. This toolchain is
incompatible with our Arduino, but it has a convenient scripting language (Duck
script) for developing keyboard payloads which we'll want to use (lost
opporunity here: Duck script does not use duck typing). Instead of using the
binaries that this toolchain produces directly to flash our devices, we'll
translate them into Arduino script. First get the Rubber Ducky toolchain from
Hak5:

```bash
git clone https://github.com/hak5darren/USB-Rubber-Ducky
cd USB-Rubber-Ducky
```

Now we need to write some Duck Script. If you're using Mac, I've written one for you. You can get it liked this:

```bash
curl -fL http://cs.iit.edu/~khale/class/security/s20/handout/hacked.osx > test.duck
```
If you don't have a Mac, grab an example script from [here](https://github.com/hak5darren/USB-Rubber-Ducky/wiki/Payloads) (the reverse shells
are particularly fun to play with). Now we need to use the Rubber Ducky
toolchain to encode this into binary:

```bash
java -jar Encoder/encoder.jar -i test.duck -o test.bin -l us
```

This encodes the script using an US English keyboard layout.

Since we don't have an [actual rubber ducky](https://shop.hak5.org/products/usb-rubber-ducky-deluxe), we use a Python tool to translate between rubber ducky binary files to Aruduino IDE source:

```bash
cd ..
git clone https://github.com/mame82/duck2spark
cd duck2spark
./duck2spark.py -i ../USB-Rubber-Ducky/test.bin -l 1 -f 2000 -o sketch.ino
```

This gives us an Arduino sketch which we can paste directly into the Arduino IDE, and program the device just like we did before.

Include screenshots and description of your working Rubber Ducky.

### Task 3: Profit
Go explore some existing [payloads](https://github.com/hak5darren/USB-Rubber-Ducky/wiki/Payloads) and see what you can get the thing to do! Record your findings in your lab handout. 

## Handin
Please write your lab report according to the description. Please also list the
important code snippets followed by your explanation. You will not receive
credit if you simply attach code without any explanation. Upload your answers
as a PDF to blackboard


## Suggested Reading
- A [video guide](https://www.youtube.com/watch?v=fGmGBa-4cYQ) which may help.
- [Duckhunt](https://github.com/pmsosa/duckhunt), a counter-measure
- A related and more sophisticated attack: BadUSB (see the [wiki](https://opensource.srlabs.de/projects/badusb/wiki) and [DefCon talk](https://www.youtube.com/watch?v=nuruzFqMgIw))

