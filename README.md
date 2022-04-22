# read APS inverters
This project is for reading APS Systems inverters. The program can pair and poll YC600 QS1 and DS3 inverters, up to 9 pieces. The read values are displayed on a web page and sent via mosquitto in a Json format.
The program has a lot of smart features. All settings can be done via the webinterface. It has a console that can be used for debugging and sending/receiving zigbee messages.

See it in action on youtube: https://youtu.be/7ZOAcrYXxbM

## downloads
april 21 2022: There is a new version v9_2 available, suitable for the new DS3 inverter. Some bugs repaired.

Download ESP-ECU-v9_2 https://1drv.ms/u/s!AkMG5FvUwhedizsQis3vYaFpLy4w?e=jKA4An<br>
Download ESP-ECU-v8_0 https://1drv.ms/u/s!AkMG5FvUwhediyjH3zbxd24_30wd?e=PsvH5L
<br><br>In case someone wants to print the housing, here is an stl: https://1drv.ms/u/s!AkMG5FvUwhedim8a_tVLywAivVDI?e=XIwhYg
This is for a nodemcu board 31x58mmr>

## features
- Easy wifi config portal
- Easy add, delete and pair inverters
- automatic polling or on demand via mqtt or http
- data can be requested via http and mosquitto
- There are 2 different json formats, a special one for domoticz
- Fast asyc webserver
- a very smart on-line console to send commands and debug
- Smart timekeeping
- A lot of system info on the webpage

## compile this sketch
Use arduino ide with the esp822 version 2.7.1 installed under boardmanager. The ota updates won't work with other versons.

Or use the binary file ESP-ECU-v6_12 to flash directly on your ESP device.

## the hardware
It is nothing more than an esp device like nodemcu, wemos or its relatives and a prepared cc2530, cc2531 zigbee module. And a powersupply.
The zigbeemodule should be flashed with a firmware that can be found here https://github.com/Koenkk/zigbee2mqtt/issues/4221. Here you can find much more information on this project.
The most recent link to a lua implementation (including firmware) by kadszol :https://github.com/Koenkk/zigbee2mqtt/files/6797510/discord-11-7-2021.zip

the wiring:
### cc2530 -> ESP 
-  p2   -> d8
-  p3   -> d7
-  vcc  -> 3.3v
-  gnd  -> gnd
-  rst  -> d5

### cc2531 - > esp (not tested by me!)
- dd -> d8 
- dc -> d7 
- rst -> d5
- vcc -> 3.3V
- gnd -> gnd

![minhw](https://user-images.githubusercontent.com/12282915/138685751-98112dfd-8ed8-4185-9de7-c2e2e8f005a9.jpg)
## how does it work
The APS system works with a separate zigbee implementation. The ESP-ECU sends zigbee requests to the inverters and analyzes the answers, extracting the values. 
The ESP communicates with the zigbee module through the alternative serial port.
The ESP-ECU starts a coordinator (an entity that can start a zigbee network). The coordinator binds the inverters and sends the poll requests to them.
The interesting values are send via mqtt and displayed on the main page.

![frontpage](https://user-images.githubusercontent.com/12282915/138686152-3b065a7c-88f8-49d6-bf89-c512f82fd562.jpg)
![graph2](https://user-images.githubusercontent.com/12282915/139062602-71e92216-9703-4fc4-acc6-fabf544c4ffd.jpg)

## known issues
The zigbee module crashes sometimes during the pollings. This is related to the polling rate, at a rate of 5 minutes (the APS standard) this almost never happens. Anyway, the system recovers that by resetting the zb module and restart the coordinator. This is done by a healthcheck that runs every 10 minutes. You can only notice this via the log and infopage where this is counted.

## changelog ##
version ESP-ECU_V9_2:
-made it suitable for the DS3 inverter.
-fixed polling intervall of 5 minutes.
-bug repaired, sq1 dc data panels 2 and 3

As of version 8 the value of total energy is no longer directly comming from the inverter's register. Instead all energy increases are added up in a total energy value. 
This way that value is not sensitive to inverter resets that will happen in twilight or due to an eclips etc.
