
# Waft

## Get better ambience and sleep temperatures by real-time syncing air conditioner with IR controlled ceiling fan.

For companion android app check [Waft Remote](https://github.com/noriutsugi/waft-remote) (Prototype)

## Description
Waft seeks to maintain consistent apparent “feels like” temperature by syncing any IR fan speed with any IR air conditioner realtime. Not only does the waft provide better ambience, it also saves power consumption as the fan increases temperature loss of body so higher temperature value can be set on the air conditioner. Waft varies fan speed to compensate for temperature fluctuation produced during air conditioner usage. Waft maintains a temperature which the user sets called apparent temperature, it is the combination of real temperature change by air conditioner and apparent temperature drop by fan.

Waft is a Wi-Fi based smart controller which is to be kept in line of sight of both air conditioner and ceiling fan. It sets the temperature of the air conditioner  and speed of the fan with infrared NEC protocol. Waft set target-temperature can be changed by air conditioner IR remote or Waft Remote Android app via REST API. Remote App can set target temperature, custom sleep mode, and different power saving modes. Waft comes in two models; Waft Core and Waft Pro. They both have above mentioned features of paragraph 1, while Waft Pro adds display, motion sensing, color and sound feedback for better user interaction.

Temperature management is included in Waft Core, whereas Waft Pro adds segment display, motion detection, interactive color, and sound feedback. The sleep mode is dynamic i.e., the user can change its behavior.

## Timeline
2 weeks (December 2, 2022 - December 16, 2022)

<hr>

## Schematics and Prototype
<p float="left">
<img height="600" alt="waft pro schem" src="https://user-images.githubusercontent.com/89692776/209439701-dd69c209-35fc-4c6e-aeac-d9f94065a8c3.png">
<img height="600" alt="waft core schem" src="https://user-images.githubusercontent.com/89692776/209439695-d29ae2cf-408c-46a7-8980-883b4cd3ae03.png">
</p>
Fig. Waft Pro schematic (left) and Waft Core schemantic (right)
<br />

<p float="left">
<img height="450" alt="waft pro" src="https://user-images.githubusercontent.com/89692776/209439597-b4edb9ab-9784-4cf9-bd0c-3db2465a9e93.jpg">
<img width="600" alt="waft core" src="https://user-images.githubusercontent.com/89692776/209439602-17c657c0-1594-48c2-b927-524b519b259f.jpg">
</p>
Fig. Waft Pro prototype (left) and Waft Core prototype (right)
<br />
<br />

<hr>

## Labelled block diagram
<img width="1200" alt="waft bld" src="https://user-images.githubusercontent.com/89692776/209439609-a977b328-fece-4525-b28c-b719a1b7b337.png">

## Waft specifications:
### Chip: 
ESPHome firmware flashed on both NodeMCU-32S and ESP-01 which supports configuration with yaml. Both connect to users Wi-Fi router/hotspot and go into AP mode when no connection is available. Uses Wireless communication to communicate with users via Waft Remote app or  integrates to Home Assistant server for debugging. Also runs a web server which provides a web api for Waft Remote App.
### Temperature: 
HTU21D used to benchmark DHT20(AHT20), BMP180, BMP280. BMP280 was closest to HTU21D while testing and about 1/4th of its price. Used to feed real-time ambient temperature.
### Infrared COMMS: 
Receiver and transmitter pair used to track and control state of air conditioner and fan.
### Motion: 
Used to device into power saving mode when enabled, also to reset sleep temperature when user wakes up due to temperature discomfort at night.
### Display, RGB, Buzzer, LDR: Display: 
Provide better interaction by showing various states such as set-temperature, real-time temperature,LED: Different colored output for different operations such as heating, cooling, sleep mode. Buzzer for input/output sound feedback. LDR: for readjusting brightness of Display, RGB based on ambience, can also be used for sleep mode hint.

## Inputs:
### Waft Remote App: 
Uses web api to configure or toggle controls of ESP modules. Supports setting/outputs of target temperature (apparent temperature), dynamic sleep mode, power saving modes.
### Air conditioner remote: 
Can be used to set target temperature, switch on/off air conditioner.

<hr>

## Overview:
When the air conditioner is only working(fan is in off state), we set the air conditioner set temperature to the required temperature we want, as the AC refrigeration cycle on fixed speed generates fluctuation in both higher and lower than the set temperature. There is also a drastic change in temperature when AC starts cooling, or when power is cut, or when the sudden increase in air exchange from outside.
Normally when we use a fan with an air conditioner, the apparent temperature is lowered and there is power savings but, there is equal or more apparent temperature fluctuation which comes from the air conditioner as apparent temperature drop varies with temperature.
So we maintain the uniform temperature by varying the apparent temperature drop created by the fan by switching it to different fan speeds realtime.
We here create a self balancing system, it tries to achieve target apparent temperature to its best independent of system in real-time.
<br />
<img width="1200" alt="graph" src="https://user-images.githubusercontent.com/89692776/209439613-7fcd3b47-a298-47b5-a8ce-7ced3c2bba5c.png">

## How do we set real-time air conditioner temperature and IR fan speed?
### For Air conditioner set-temperature:
[1]We calculate peak air speed of fan at different RPM by knowing fan diameter and air delivery.
<br />
[2]We calculate the weighted area average of the room by square room length, sitting height, blade height from floor, and fan diameter.
<br />
[3]We use [2] and [1] to find effective fan speed in a room at seated height.
<br />
[4]We calculate apparent temperature decrease of the fan by 1 m/s by different RPM at real-time air  temperature.
<br />
[5]We use [3] and highest apparent drop and remove peak temperature deviation to find temperature to be increased while considering power saving settings.
<br />
[6]We find the set-temperature for the air conditioner by subtracting [5] from target apparent temperature.

### For IR fan speed number:
[7]We calculate the required apparent temperature to be dropped by subtracting target temperature from real-time temperature.
<br />
[8]We use [4] and speed number to RPM chart to determine speed required.
<br />
<img width="1200" alt="algo" src="https://user-images.githubusercontent.com/89692776/209439638-4372fff3-54ec-404a-871a-7a811bcde1f9.png">
