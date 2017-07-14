# HomeAssistant Xiaomi Hub Component by Rave (Lazcad)

Credits
---------------
Credits to the following Github project
- https://github.com/fooxy/homeassistant-aqara
- https://github.com/louisZL/lumi-gateway-local-api

Description
---------------
This is an complete Home Assistant component for Xiaomi Gateway. It allows you to integrate the following devices into HA

- Temperature and Humidity Sensor
- Motion Sensor
- Door and Window Sensor
- Button
- Plug aka Socket (ZigBee version, reports power consumed, power load, state and if device in use)
- Wall Plug (reports power consumed, power load and state)
- Aqara Wall Switch (Single)
- Aqara Wall Switch (Double)
- Aqara Wireless Switch (Single)
- Aqara Wireless Switch (Double)
- Cube
- Gas Leak Detector (reports alarm and density)
- Smoke Detector (reports alarm and density)
- Gateway (Light, Illumination Sensor, Ringtone play)
- Battery
- Curtain

What's not available?

- Gateway Radio
- Gateway Button
- Door and Window Sensor (new version, square)
- Button (new version, square)
- Temperature and Humidity Sensor (new version, square)
- Motion Sensor (new version with illumination sensor and holder)
- Intelligent Curtain
- Aqara Air Conditioning Companion
- Aqara Intelligent Air Conditioner Controller Hub
- Decoupled mode of the Aqara Wall Switche (Single & Double)
- Additional alarm events of the Gas and Smoke Detector: Analog alarm, battery fault alarm (smoke detector only), sensitivity fault alarm, I2C communication failure

Installation (Raspberry Pi)
---------------------------

1. First, copy all the files into the Home Assistant location. It can now be installed either to the custom_components folder 
 ```
 /home/homeassistant/.homeassistant/custom_components
 ```
 or the root folder (using virtual environment)
 ```
 /srv/homeassistant/homeassistant_venv/lib/python3.4/site-packages/homeassistant/components
 ```

2. Add the following line to the Configuration.yaml. Make sure you're on the latest firmware. You will need to get the Hub's key in order to issue command to the hub like turning on and off plug. Follow the steps here https://github.com/louisZL/lumi-gateway-local-api/blob/master/device_discover.md

 One Gateway
  ```yaml
 #you can leave sid empty if you only have one gateway
 xiaomi:
   gateways:
     - sid:
       key: xxxxxxxxxxxxxxxx
  ```

 Multiple Gateway
  ```yaml
 #12 characters sid can be obtained from the gateway's MAC address.
 xiaomi:
   gateways:
     - sid: xxxxxxxxxxxx
       key: xxxxxxxxxxxxxxxx
     - sid: xxxxxxxxxxxx
       key: xxxxxxxxxxxxxxxx
  ```

3. Start HA. Pycrypto should install automatically. If not, install pycrypto manually. if you are using virtual environment, remember to install from virtual environment like below
 ```
 (homeassistant_venv) pi@raspberrypi:~ $ pip3 install pycrypto
 ```

4. Add friendly names to the Configuration.yaml like below
  ```yaml
    customize:
      binary_sensor.switch_158d000xxxxxc3:
          friendly_name: Ktichen Switch
      binary_sensor.switch_158d000xxxxxc2:
          friendly_name: Table Switch
      binary_sensor.door_window_sensor_158d000xxxxx7a:
          friendly_name: Door Sensor
  ```

5. Add automation. For the Button and Switch, use the following event. Available click types are 'single', 'double' and 'hold'. For door window sensor and motion sensor, you can use State or Event as trigger
  ```yaml
  automation:
  - alias: Turn on Dining Light when click
    trigger:
      platform: event
      event_type: click
      event_data:
          entity_id: binary_sensor.switch_158d000xxxxxc2
          click_type: single
    action:
      service: switch.toggle
      entity_id: switch.wall_switch_left_158d000xxxxx01
  ```

  ```yaml
    #trigger for motion sensor using State

    trigger:
      platform: state
      entity_id: binary_sensor.motion_sensor_158d000xxxxxc2
      from: 'off'
      to: 'on'

    #trigger for motion sensor using Event

    trigger:
      platform: event
      event_type: motion_action
      event_data:
          entity_id: binary_sensor.motion_sensor_158d000xxxxxc2
          action_type: motion # motion / no_motion
  ```

  ```yaml
    #trigger for door window sensor using State

    trigger:
      platform: state
      entity_id: binary_sensor.door_window_sensor_158d000xxxxxc2
      from: 'off'
      to: 'on'

    #trigger for door window sensor using Event

    trigger:
      platform: event
      event_type: door_window_action
      event_data:
          entity_id: binary_sensor.door_window_sensor_158d000xxxxxc2
          action_type: open # open / close / no_close
  ```

6. For Cube, use the following trigger. Available actions are flip90, flip180, move, tap_twice, shake_air, swing, alert, free_fall and rotate

 ```yaml
    trigger:
      platform: event
      event_type: cube_action
      event_data:
          entity_id: binary_sensor.cube_158d000xxxxxc2
          action_type: flip90
 ```

7. Important! Only use this if you have have issue with Socket binding or multicast. Using this when you have no problem with socket or mcast will introduce other issues. Add the IP address of the network interface to the config
 
 ```yaml
 xiaomi:
   gateways:
     - sid: xxxxxxxxxxxx
       key: xxxxxxxxxxxxxxxx
     - sid: xxxxxxxxxxxx
       key: xxxxxxxxxxxxxxxx
    interface: xx.xx.xx.xx
 ```
