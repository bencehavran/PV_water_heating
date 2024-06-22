# PV_water_heating
Using Photovoltaic Energy for Water Heating
<img src="https://github.com/bencehavran/PV_water_heating/assets/64528618/e37bdf65-1786-4cb9-a537-110f9ae944dc" width="750" height="400">
<img src="https://github.com/bencehavran/PV_water_heating/assets/64528618/64499a64-3502-44a1-b8be-09d865c177b9" width="300" height="580">

Using Excess Photovoltaic Energy with Shelly Devices for Water Heating
Harnessing excess energy generated by your photovoltaic (PV) system can be simplified using Shelly devices, allowing you to efficiently utilize this energy within your home rather than feeding it back into the grid. This method can be particularly effective for heating water in a boiler without requiring extensive technical expertise. Here's how you can achieve this.

Important Safety Notice
=============
WARNING!
Working with electricity can be dangerous. If you're not confident in your understanding of electrical systems or DIY projects, please consult a professional or refrain from attempting this setup. I take no responsibility for any injuries or damages resulting from following these instructions.

**Required Equipment**
1 x Shelly 1 PM
[Amazon Shelly 1 PM ](https://www.amazon.de/-/en/Bluetooth-Current-Measurement-Automation-Required/dp/B0965J4HT5/ref=sr_1_1?sr=8-1)

2 x Shelly 1[ Amazon Shelly 1 ](https://www.amazon.de/-/en/Shelly-1-Wireless-Switching-Actuator/dp/B084H3H3NT/ref=sr_1_30?sr=8-30)

1 x Shelly 3EM Pro or Shelly 3EM[ Amazon Shelly 3EM ](https://www.amazon.de/-/en/Controlled-Intelligent-Measurement-Compatible-Monitoring/dp/B0918NR3YC/ref=sr_1_2_mod_primary_new?sbo=RZvfv%2F%2FHxDF%2BO5021pAnSA%3D%3D&sr=8-2)
1 x AC Voltage Regulator 230V[Amazon voltage Regualtor] (https://amzn.eu/d/0i7waX59)




**Overview**
Shelly 3EM Pro: Monitors the real-time consumption and input of the three phases.

Shelly 1 PM: Controls the heater system's power state and measures the input.

Shelly 1: Used to control the voltage regulator's activation and deactivation.


"Wiring Diagram.jpg" LATER


Configuration Steps
Once you have connected everything according to the wiring diagram, there are two primary methods for controlling the system:

Simpler Method: Using the Shelly App
Setup in Shelly App:

Use the Shelly app to monitor the overall power flow across the three phases.
Configure the Shelly 1 UP to activate when the sum of the three phases is negative (indicating excess energy).
Configure the Shelly 1 DOWN to activate when the sum is positive and reaches or exceeds a certain threshold.
Automatic Switch-Off:

In the Shelly app, set both Shelly 1 devices to automatically switch off after 0.5 seconds.
This ensures that the Shelly 1 devices only send a brief signal to the voltage regulator, effectively turning it on or off as needed.


Advanced Method: Using Home Assistant
For more robust, local control that doesn't rely on internet connectivity, you can integrate this setup with Home Assistant:

Home Assistant:

Assume you already have Home Assistant running and have some experience with it. If not, refer to the Home Assistant website for setup guidance.
You can create automations within Home Assistant to monitor the energy data from the Shelly devices and control the voltage regulator based on real-time measurements.
Scripting:

create an automatism in homeassistant and insert it. exceeding -52 values ​​turns on shelly1 up, modify it with your shelly's ID and the shelly 3em sensor!

```alias: Boiler heating up 
description: boiler heating up
trigger:
  - platform: numeric_state
    entity_id: sensor.YOUR.SHELLY.1.UP
    below: "-52"
    for:
      hours: 0
      minutes: 0
      seconds: 4
condition: []
action:
  - repeat:
      while:
        - condition:
            - condition: numeric_state
              entity_id: sensor.YOUR.SHELLY.3EM
              below: "0"
      sequence:
        - type: turn_on
          device_id: 7e9a14931c2528ef19bf6838c96cefee
          entity_id: d43569b6af79f2d72e1cd4cb5f7105e2
          domain: switch
        - delay: "00:00:3"
mode: single
```
and this is the automatism for turn down:

```alias: Boiler heating DOWN
description:  Boiler heating DOWN
trigger:
  - platform: numeric_state
    entity_id:  sensor.YOUR.SHELLY.3EM
    above: "62"
    for:
      hours: 0
      minutes: 0
      seconds: 5
condition: []
action:
  - repeat:
      while:
        - condition: and
          conditions:
            - condition: numeric_state
              entity_id: sensor.YOUR.SHELLY.1.DOWN
              above: "6"
            - condition: numeric_state
              entity_id: sensor.power_total
              above: 62
      sequence:
        - type: turn_on
          device_id: YOUR.SHELLY.1.UP
          entity_id: YOUR.SHELLY.1.UP
          domain: switch
        - delay: "00:00:02"
mode: single
```
This method provides more precise control and reduces dependency on cloud services.
Final Notes

Using Shelly devices in this way allows you to maximize the utility of your PV system's excess energy, contributing to more efficient and sustainable home energy management. Be sure to follow all safety guidelines and consult with a professional if you have any uncertainties about the installation process.

For further details on integrating with Home Assistant, please stay tuned for a detailed script guide, or explore the resources available on the Home Assistant website.

By following these instructions, you can effectively and efficiently use your excess photovoltaic energy for heating water, reducing reliance on the grid and lowering your utility costs.






