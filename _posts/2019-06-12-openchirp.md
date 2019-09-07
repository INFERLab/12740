---
layout: post
title: 4. Creating Your Own IoT Device
permalink: /tutorials/openchirp.html
---

In this tutorial, we will show you how to set up your own IoT device. To achieve that, you  will
- Create a device on [OpenChirp](https://openchirp.io/)
- Publish/subscribe data using MQTT protocol

### Create a Device on OpenChirp
We will use the LED controlled by ambient lighting from the previous tutorial as example.

- You should create a device following this [tutorial](https://github.com/OpenChirp/docs/wiki/simple-device-tutorial), except for setting up **Transducers** and **Commands** specific to the example, as below:  

#### Transducers

|**Name**|**Unit**      |**Actuable**|
| ------------- | ---------------------|---------|
| light-dependent_resistor  | Voltage|     false|  
|   light | Binary     | true|

#### Commands

|**Command**|**Value**      |**Transducer**|
| ------------- | ----|--------|
| Light On| 1| light|       
|   Light Off | 0  | light|  

- You can see value changes of the transducers in the **Visualization** tab. *(Issue commands to change the actuator state. You will learn how to modify the sensor readings in the next section.)*

![visualization](/12740/assets/Openchirp.png)

- For more information on OpenChirp, refer to [this paper](/12740/assets/OpenChirp.pdf)


### Publish/Subscribe Data using MQTT Protocol
Read [here](https://randomnerdtutorials.com/what-is-mqtt-and-how-it-works/) for a simple introduction on what is MQTT communication protocol. 

- Install **paho-mqtt** library
```
$ pip3 install paho-mqtt
```
- Create a MQTT client for your device, following the template code below. You should use the **Device ID** and **Token** of the device you just created. 

```python
import paho.mqtt.client as mqtt

# MQTT client
class Device(mqtt.Client):
    def __init__(self, username, password):
        super(Device, self).__init__()
        self.host = "mqtt.openchirp.io"
        self.port = 8883
        self.keepalive = 300
        self.username = username
        self.password = password
        
        # Set access credential
        self.username_pw_set(username, password) #set username and pass
        self.tls_set('cacert.pem')
        
        # Create a dictionary to save all transducer states
        self.device_state = dict()
        
        # Connect to the Broker, i.e. OpenChirp
        self.connect(self.host, self.port, self.keepalive)
        self.loop_start()
    
    def on_connect(self, client, userdata, flags, rc):
        if rc == 0:
            print("Connection Successful")
        else:
            print("Connection Unsucessful, rc code = {}".format(rc))
        # Subscribing in on_connect() means that if we lose the connection and reconnect, the subscriptions will be renewed.
	# Subscribe to all transducers
        self.subscribe("openchirp/device/"+self.username+"/#") 

    # The callback for when a PUBLISH message is received from the server.
    def on_message(self, client, userdata, msg):
        print(msg.topic+" "+str(msg.payload.decode()))
        
	# Change Actuator State based on Commands Issued from OpenChirp
        transducer = msg.topic.split("/")[-1]
        self.device_state[transducer] = msg.payload.decode()

    def on_publish(self, client, userdata, result):
        print("Data published")

# Modify here based on your own device
username = '5d488468466cc60c381e0b5e' # Use Device ID as Username
password = 'D1vmt0VIWDiVfTvoxn7rnGBMrgCZEO7a' # Use Token as Password

# Instantiate a client for your device
smart_light = Device(username, password)
```
- Now you can publish sensor readings onto OpenChirp, which you can see in the **Visualization** tab. At the same time, you can issue commands from OpenChirp to control your device. See `Tutorial-4.py` for the complete code. 

```python
def main():
    threshold = 2 # Threshold for turn on/off LED
    sensor = "light-dependent_resistor"
    actuator = "light"
    
    # Initialize
    smart_light.device_state[sensor] = 0
    smart_light.device_state[actuator] = 0
    
    while True:
	# Actuate the light based on the command
        GPIO.output(red_led, int(smart_light.device_state[actuator]))
        
        # Read from sensor and Publish onto OpenChirp
        sensor_reading = channel.voltage
        smart_light.publish("openchirp/device/"+username+"/"+sensor, payload=sensor_reading, qos=0, retain=True)
        
	# Update device state
        smart_light.device_state[sensor] = sensor_reading

	time.sleep(1)
```
### **Good job! You have successfully set up your own IoT device.** 




