---
layout: post
title: 4. Publishing Your Sensor Readings on OpenChirp
permalink: /tutorials/openchirp.html
---

In this tutorial, we will:
- Show you how to publish sensor readings on [OpenChirp](https://openchirp.io/)

- Set up a simple device following this [tutorial](https://github.com/OpenChirp/docs/wiki/simple-device-tutorial)

Transducers
|**Photosensitive Light Sensor Module**|      |
| ------------- | ---------------------|
| VCC  | RPi 3.3V|       
|   GND | RPi GROUND     | 
| AO   | MCP3008 CH0 (Pin 1) | 


Commands
|**Photosensitive Light Sensor Module**|      |
| ------------- | ---------------------|
| VCC  | RPi 3.3V|       
|   GND | RPi GROUND     | 
| AO   | MCP3008 CH0 (Pin 1) | 


```
$ pip3 install paho-mqtt
```

Read [here](https://randomnerdtutorials.com/what-is-mqtt-and-how-it-works/) for a simple guide on the MQTT communication protocol. 