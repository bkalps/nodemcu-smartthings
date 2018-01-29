# nodemcu-smartthings
My personal notes and lessons from heythisisnate's repository
INTRODUCTION
I came across this idea on the SmartThings message boards:
https://community.smartthings.com/t/convert-your-wired-alarm-system-into-a-smartthings-smart-home-monitor-system-for-under-25-with-nodemcu-esp8266-2017/84285

It led me to the user's (Nate Clark) link, who since then turned this into a company called Konnected.
https://github.com/heythisisnate/nodemcu-smartthings
SmartThings Connected Wired Security System using a NodeMCU ESP8266
This project will help you connect wired contact sensors and motion sensors from an old wired home alarm system (such as Honeywell, ADT, Interlogix, etc) to Samsung SmartThings. Convert your old wired alarm system into an internet connected Smart Alarm!
We use an inexpensive NodeMCU ESP82660 wifi enabled development board to connect our wired alarm system sensors to the SmartThings.
There are three components to the project:
	1. a SmartThings Device Handler for contact sensors and motion sensors
	2. a SmartThings SmartApp that interfaces with the Wifi-connected device in your home
	3. Lua code for the NodeMCU device that connects your wired system to the cloud
