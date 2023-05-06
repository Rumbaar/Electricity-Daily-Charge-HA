# Electricity Daily Usage - Home Assistant [Energy Dashboard]
Calculation of a static daily rate change for Electricity over 24 hours, inline with the hourly metric of the energy dashboard in Home Assistant.

As of Home Assistant 2023.5 the inclusion of a daily flat Electricity rate within the Energy Dashboard and overall calculations isn't present or easily created to offer flat rate calculated over the 24 hour (hourly) daily dashboard.  Inspired by a hourly rate present in a locally available power monitor device (Powerpal) and hourly calcuations of HA Energy dashbaord this repository will look to create an energy sensor that will allow the input of a flat rate and then have that value split over the full day for a (at best) realtime cost.

Example for the Australian Electricity Market; suppliers charge a flat daily rate of $1.24, that is calcuated and added for each day of usage on top of the actual power usage that is calculatee per kWH of import from the grid.

## Basic steps to expand in more detail

1. Create a Helper - "Input Number" called "ElectrictyDaily" which is the base daily cost * 1000 (this is due to work around reducing the daily usage hack of 1W over 24 hours to have almost no impact to the total power metering).  Refer to input_number.yaml in the files section.
2. Create a Helper - "Input Dateime" called "DailyChargeStartTime" which is the start time used in the calculation of the '1W' over the 24 hours which is devided over the whole day and increases hourly for the hour segments in the Energy Dashboard.
3. Create an automation to reset the Date/Time at the start of the day to reset the usage calcuation for the day.
4. Add the Energy Sensor code for the "DailyCharge" entity to the appropriate configuration.yaml file.
5. Restart Home Assistant
6. Define the new "DailyCharge" sensor via your Energy Dashbaord settings under Electricity grid => Grid consumption.  It can take 24 hours for the values and figures to be correct.
