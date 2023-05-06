# Electricity Daily Usage - Home Assistant [Energy Dashboard]
Calculation of a static daily rate change for Electricity over 24 hours, inline with the hourly metric of the energy dashboard in Home Assistant.

As of Home Assistant 2023.5 the inclusion of a daily flat Electricity rate within the Energy Dashboard and overall calculations isn't present or easily created to offer flat rate calculated over the 24 hour (hourly) daily dashboard.  Inspired by a hourly rate present in a locally available power monitor device (Powerpal) and hourly calcuations of HA Energy dashbaord this repository will look to create an energy sensor that will allow the input of a flat rate and then have that value split over the full day for a (at best) realtime cost.

Example for the Australian Electricity Market; suppliers charge a flat daily rate of $1.24, that is calcuated and added for each day of usage on top of the actual power usage that is calculatee per kWH of import from the grid.
