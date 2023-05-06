# Electricity Daily Usage - Home Assistant [Energy Dashboard]
Calculation of a static daily rate change for Electricity over 24 hours, inline with the hourly metric of the energy dashboard in Home Assistant.

As of Home Assistant 2023.5 the inclusion of a daily flat Electricity rate within the Energy Dashboard and overall calculations isn't present or easily created to offer flat rate calculated over the 24 hour (hourly) daily dashboard.  Inspired by a hourly rate present in a locally available power monitor device (Powerpal) and hourly calcuations of HA Energy dashbaord this repository will look to create an energy sensor that will allow the input of a flat rate and then have that value split over the full day for a (at best) realtime cost.

Example for the Australian Electricity Market; suppliers charge a flat daily rate of $1.24, that is calcuated and added for each day of usage on top of the actual power usage that is calculatee per kWH of import from the grid.

## Basic steps to expand in more detail

1. Create a Helper - "Input Number" called "ElectrictyDaily" which is the base daily cost * 1000 (this is due to work around reducing the daily usage hack of 1W over 24 hours to have almost no impact to the total power metering).  Refer to input_number.yaml in the files section.
```yaml
{
  "version": 1,
  "minor_version": 1,
  "key": "input_number",
  "data": {
    "items": [
      {
        "id": "electricitydaily",
        "min": 0.0,
        "max": 1500.0,
        "name": "ElectricityDaily",
        "icon": "mdi:currency-usd",
        "step": 0.01,
        "unit_of_measurement": "AUD/kWh",
        "mode": "box"
      }
    ]
  }
}
```
![image](https://user-images.githubusercontent.com/84074944/236602934-6b74bd3e-8dc6-4f31-92e6-7c689256d119.png)

![image](https://user-images.githubusercontent.com/84074944/236602914-010a9213-4925-443b-bd8e-7fd82c9df4be.png)

2. Create a Helper - "Input Dateime" called "DailyChargeStartTime" which is the start time used in the calculation of the '1W' over the 24 hours which is devided over the whole day and increases hourly for the hour segments in the Energy Dashboard.
```yaml
{
  "version": 1,
  "minor_version": 1,
  "key": "input_datetime",
  "data": {
    "items": [
      {
        "id": "dailychargestarttime",
        "has_time": true,
        "has_date": true,
        "name": "DailyChargeStartTime",
        "icon": "mdi:clock-time-twelve-outline"
      }
    ]
  }
}
```
![image](https://user-images.githubusercontent.com/84074944/236603011-909859c3-4f39-41b3-a061-93f18aad32e8.png)

![image](https://user-images.githubusercontent.com/84074944/236602979-37dc30e4-7e49-478d-821d-657156c87714.png)

3. Create an automation to reset the Date/Time at the start of the day to reset the usage calcuation for the day, raw YAML in automation.yaml. You'll need to define the "Actions" code via "Edit in YAML" as the syntax required is avalable via the visual editor (as of 2023.5).
```yaml
alias: Reset DailyChargeTimer at Midnight
description: ""
trigger:
  - platform: time
    at: "00:00:00"
condition: []
action:
  - service: input_datetime.set_datetime
    data:
      datetime: "{{ (now() | string)[:19] }}"
    target:
      entity_id: input_datetime.dailychargestarttime
mode: single
  ```
  ![image](https://user-images.githubusercontent.com/84074944/236603175-9f391dfa-cf62-4e15-9ff8-d3f0052b47cc.png)

4. Add the Energy Sensor code for the "DailyCharge" entity to the appropriate configuration.yaml file.
```yaml
template:
  - sensor:
      - name: "DailyCharge"
        unique_id: "7ac7c3b4-1238-4961-9b61-cc2cc8sample"
        unit_of_measurement: "kWh"
        device_class: energy
        state_class: total_increasing
        icon: mdi:solar-power
        state: >
          {{ (((as_timestamp(states('sensor.date_time_iso'))) - (as_timestamp(states('input_datetime.dailychargestarttime')))) / 86400 * 0.001) | round(5) }}
```
5. Restart Home Assistant
6. Define the new "DailyCharge" sensor via your Energy Dashbaord settings under Electricity grid => Grid consumption.  It can take 24 hours for the values and figures to be correct.

![image](https://user-images.githubusercontent.com/84074944/236603235-3011472a-8c6e-4a51-a783-dc146422c32b.png)

![image](https://user-images.githubusercontent.com/84074944/236602812-069a9678-6892-45f0-b5c8-c5f92531cb3b.png)

