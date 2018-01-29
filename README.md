# nodemcu-smartthings
My personal notes and lessons from heythisisnate's repository

# INTRODUCTION
I came across this idea on the SmartThings message boards:
https://community.smartthings.com/t/convert-your-wired-alarm-system-into-a-smartthings-smart-home-monitor-system-for-under-25-with-nodemcu-esp8266-2017/84285

It led me to the user's (Nate Clark) link, who since then turned this into a company called Konnected.
https://github.com/heythisisnate/nodemcu-smartthings

## SmartThings Connected Wired Security System using a NodeMCU ESP8266
This project will help you connect wired contact sensors and motion sensors from an old wired home alarm system (such as Honeywell, ADT, Interlogix, etc) to Samsung SmartThings. Convert your old wired alarm system into an internet connected Smart Alarm!
We use an inexpensive NodeMCU ESP82660 wifi enabled development board to connect our wired alarm system sensors to the SmartThings.
There are three components to the project:
	1. a SmartThings Device Handler for contact sensors and motion sensors
	2. a SmartThings SmartApp that interfaces with the Wifi-connected device in your home
	3. Lua code for the NodeMCU device that connects your wired system to the cloud

# Background
The house I live in was built in the early 90s and came with a built-in home security system. I'm not interested in using the outdated alarm system panel, but I wanted to connect the contact sensors in my doors and the motion sensor in my house to SmartThings. I learned about the NodeMCU ESP8266, a small, cheap, programmable development board that has WiFi built in. I set out to connect my door and motion sensors to the NodeMCU and program it to update SmartThings every time a change is detected.

#Materials
	1. A NodeMCU development board. This is the one I bought on Amazon for about $8 with Prime shipping.
	2. A basic breadboard or 3-pack.
	3. Some extra wires of various male/female combinations.
	4. A microUSB power supply or try one of these other ways to power the device
	5. To connect your alarm siren or strobe light, you'll need a relay that the board can switch with a 3.3V signal. These work well and are only a few dollars each (ship from China).

I bought #1-4 above as a package on Amazon for $19.95: https://www.amazon.com/gp/product/B075HLCGW8/ref=oh_aui_detailpage_o05_s00?ie=UTF8&psc=1

I bought #5 on Amazon for $5.48:
https://www.amazon.com/gp/product/B01FK11HV4/ref=oh_aui_detailpage_o06_s00?ie=UTF8&psc=1

##Nate's Annotated Photo

#Step by Step Setup Guide

##Getting Started
	1. Clone or download this repository and open up the lua folder.
	2. Copy or rename variables.lua.example to variables.lua
	3. Copy or rename credentials.lua.example to credentials.lua
	4. Open up credentials.lua in your favorite text editor and put in your WiFi SSID and password
		a. When putting your wifi credentials in "credentials.lua" I think it needs to be a 2.4GHz channel wifi. The board wouldn't recognize my 5GHz channel.
	5. Also open up variables.lua. We'll be completing this as we set up things in SmartThings.

##Create Device Handler(s) in SmartThings
	1. Log in to the SmartThings IDE -> My Locations -> click on your location
	2. Go to My Device Handlers -> Create New Device Handler
	3. Click the From Code tab and paste the content of one of the device handlers and save:
	• NodeMCU Connected Contact Sensor
	• NodeMCU Connected Motion Sensor
	• NodeMCU Connected Smoke Detector
	• NodeMCU Connected Alarm
	4. Click Publish -> For Me
	5. Repeat for the other device handler if you need both types

##Create Devices in SmartThings

You'll need to create a device for each sensor that you plan on connecting. Repeat these steps for each sensor:
	1. In the SmartThings IDE, go to My Devices
	2. Click New Device and fill out the form giving your device a name like "Front Door"
	3. In the Device Type dropdown, select either the NodeMCU Connected Contact Sensor or NodeMCU Connected Motion Sensor that you created earlier.
	4. The Device Network Id doesn't seem to really matter, I just put a number.
	5. Once you've created the device, make note of the device's URL. It will be something like https://graph-na02-useast1.api.smartthings.com/device/show/22433333-1111-41dc-0000-00000000000. The last part of the url is the DeviceId. Copy this DeviceId to the variables.lua file.

If you're connecting your alarm system siren or strobe, follow these steps:
	1. Click New Device and fill out the form giving your siren a name like "Alarm"
	2. In the Device Type dropdown, select NodeMCU Connected Alarm
	3. Put any unique string in for Device Network Id. This will be overwritten later when you connect.

##Table of Organization
(Table goes here.)

##Create the SmartApp

The SmartApp receives data from your NodeMCU device, and updates the status of your devices in SmartThings.
	1. Go to My SmartApps -> New SmartApp
	2. Click the From Code tab and paste the content of the SmartApp:
		○ Cloud Sensor
		○ (Now that I have the glass break sensor, I should look to add that also)
	3. Once the SmartApp is created, click the edit icon or go to App Settings -> OAuth and enable OAuth and save.
	4. Make note of the OAuth Client ID and Client Secret, you'll need these later.
		a. OAuth Client ID: 92a21ef0-76fb-4eda-b81d-ed44d69e690c
		b. OAuth Client Secret: 1b3913ea-2290-4260-88a4-e3e89590d981
	5. Click Publish -> For Me

##Flash the NodeMCU Lua firmware

###Drivers
Windows and Mac users will need to download drivers so your computer can talk to the ESP8266 chip over USB. Depending on which board you have, there are different drivers:
WeMos CH340 drivers for boards that:
	• have the name LoLin on the back or front
	• the small rectangular component on the board near the USB port is engraved with CH340G
	• Mac OS X Sierra users: use this driver

Brian: For a while, I didn't think this worked, but in reality it worked fine, but the microUSB power cable that came in the kit must have been bad. I swapped it with my Logitech microUSB cable and the MacBook recognized the device!

###Firmware
	1. The firmware contained in this repo is a recent build from https://nodemcu-build.com/ with following packages: file, cjson, GPIO, HTTP, net, node, timer, UART, WiFi and TLS/SSL support. This firmware is on SDK version 1.5.4.1. Between the time that I did this project and wrote up this README, the NodeMCU firmware team has released a 2.0.0 firmware which has a bug with SSL/TLS and this program doesn't work. Until this is fixed, you must use the 1.5.4.1-final branch if you're building your own firmware at https://nodemcu-build.com/. More info in https://github.com/nodemcu/nodemcu-firmware/issues/1707.
	2. I used esptool.py to flash the firmware (I'm using a Mac). There's pretty good documentation here including a couple other options for Windows users.
	3. The exact command the author used to flash his firmware was:
		a. esptool.py --port=/dev/cu.SLAB_USBtoUART write_flash --flash_mode dio 0x00000 firmware/nodemcu-1.5.4.1-final-10-modules-2017-03-23-20-42-52-integer.bin
		b. The port may vary depending on your platform, OS and UART driver.
			i. My port: cu.wchusbserial1420
			ii. Mode: dio for >=4 MByte modules (qio might work as well, YMMV).
			iii. Firmware: firmware/nodemcu-1.5.4.1-final-10-modules-2017-03-23-20-42-52-integer.bin
			iv. Start with sudo to have admin privileges, and use complete pathnames depending on where you are in the Terminal
	4. The exact command I used to flash my firmware was:
		a. sudo python esptool-master/esptool.py --port=/dev/cu.wchusbserial1420 write_flash --flash_mode dio 0x00000 nodemcu-smartthings-master/firmware/nodemcu-1.5.4.1-final-10-modules-2017-03-23-20-42-52-integer.bin

##Load up the NodeMCU ESP8266
	1. Download Esplorer. It's a cross-platform IDE for interacting with the NodeMCU. Very handy.
	2. Use the instructors here (http://esp8266.ru/download/esp8266-doc/Getting%20Started%20with%20the%20ESPlorer%20IDE%20-%20Rui%20Santos.pdf) and simply use this command line in your terminal to run the ESPlorer: "sudo java –jar ESPlorer.jar"
	3. Plug a microUSB cable into the NodeMCU and the other end into your computer, open up Esplorer, select the USBtoUART from the serial port chooser, set the baud rate to 115200, and click Open to connect. You may need to click the RTS button a couple times to connect and see something like this:
(Image)
	4. Once connected, it's time to upload the code. Click the Upload button in ESplorer and navigate to the lua directory. Highlight all the lua and html files and click Open to upload them to the device:
(Image)
	5. After all the code is uploaded, toggle the RTS button on then off to restart the device. It should boot up, connect to your WiFi and output a link to begin the OAuth flow.
	6. Copy and paste the OAuth link URL into your web browser and begin the OAuth flow. You'll need the OAuth Client ID and Secret from the SmartApp.
		a. OAuth Client ID: 92a21ef0-76fb-4eda-b81d-ed44d69e690c
		b. OAuth Client Secret: 1b3913ea-2290-4260-88a4-e3e89590d981
	7. After you enter the Client ID and Secret, you'll see a page like this allowing you to authorize the devices you set up earlier:
(Image)
	8. Authorize all the NodeMCU connected devices. When prompted, reboot the device by toggling the RTS button in ESPlorer on and off.
	9. During testing, the balcony door pin was spotty (initially set to D9, there was no D9, so tried D8 and now it's D4… and now D5)
		a. Forgot not to use D4 because it's tied to the LED on the board. Noticed D5 wasn't being used.

##Connect your switches at the alarm panel
	1. Open up your alarm panel and find the cluster of wires coming from the switches throughout your house. Hopefully they're labeled well, otherwise you'll have some testing to do. Using some jumper cables, connect each pair of wires from each switch to the corresponding pin and ground on the board.
		a. Nate recommends removing all the resistors. I kept one on for the Airbnb Window because it seemed to work better with it.
		b. If possible, use a breakout board or crimp connectors for stranded wires. I had the screw caps.
		Breakout Board
		(Image)
		 A breakout board like this one gives you a clean and organized approach. The breakout board converts pins into screw terminals that work just fine with stranded wire. Here’s a larger breakout board for installs with more than 8 zones.
	2. Now go around your house testing it out!
	3. Once everything is working properly, you can plug the NodeMCU into a standard USB power adapter and it will automatically boot up, connect to WiFi, and start listening for switches.
		a. Or, like me, I powered it from my existing Alarm Panel since it looks cleaner:
		Powering the Device from an Existing Alarm Panel
		Most traditional alarm panels output 12V DC power from two screw terminals labeled AUX Output. This is used to power your home’s motion sensors, keypad, glass-break sensors, etc. You can tap into this power output to power your Konnected device. Use your alarm panel’s wiring diagram or user’s manual to identify the 12V DC AUX Output terminals.
		 https://docs.konnected.io/security-alarm-system/wiring/power/
	4. Random message board details…
		a. The "PIR" are going to be motion detectors; most of these will have 4 wires, 2 of these (in your case black & red) are going to an auxiliary power input that is powering the motion detector. The other 2 wires (green & yellow in your picture) are going to be the open/close loop.
		b. Any hardwired door/window contacts will be wired similar to the green/yellow wire, they just open/close the circuit when the door is open/closed. Not sure if your contacts are normally open, or normally closed (they can be either). The resistors (hot dog shaped things with stripes) are in place to give you a normal voltage drop across the wires. From your picture, on the supervised pins, in a normal situation, should be 2.5 to 8.5 VDC.

##Siren stuff eventually
https://docs.konnected.io/security-alarm-system/wiring/siren/
