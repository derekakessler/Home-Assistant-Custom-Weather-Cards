![weather-cards-home](https://github.com/derekakessler/Home-Assistant-Custom-Weather-Cards/assets/1646121/fc0c2492-711f-4ba9-b7cd-7917566b2446)
![weather-cards-search](https://github.com/derekakessler/Home-Assistant-Custom-Weather-Cards/assets/1646121/449a2f77-4c40-49b7-b631-b348b4ea9414)

### What is this?

- data source: Pirate Weather
- current weather: button-card pulling on the PW sensor attributes to fill out the custom_fields options
- forecasts: markdown card with overflow-scrolling table of time/date, temps, conditions, precipitation%, dew point, and wind speed
- styling:
  - current weather: button-card styles with JS checking the conditions to call a background-image that is overlaid on a color to match the temperature (cold blue to comfortable green to hot red)
  - forecasts: card-mod to apply CSS to the tables and generate swoopy SVG background ribbons of the temperature gradient (daily is highs and lows) and the precipitation accumulation for that period (the vertical lines).
- radar: iframe card of Windy.com

The "Home" tab is a fixed "home" location for a Pirate Weather sensor that updates every 10 minutes. This location never changes.

The "Local" tab pulls the latitude and longitude from a device tracker and inserts that into the REST API call for a separate PW sensor and into an auto-entities card to generate a new iframe card for the radar. The PW sensor updates every 10 minutes.

The "Search" tab is a special one. The location is an input_text field. When changed it triggers a Node-Red flow through a Google Maps geocoding node to get the coordinates, which are then sent to a separate input_text for storage and references in a PW sensor and the auto-entities iframe radar. When the coordinates are updated the PW sensor receives a refresh command, otherwise it updates once an hour or when the refresh button is pressed.

![screenshot 2024-02-24 at 21 00 13](https://github.com/derekakessler/Home-Assistant-Custom-Weather-Cards/assets/1646121/784fa1b4-2610-439f-b9fc-f9ec70bda461)

This is all driven by the Pirate Weather sensor attributes and the incredible amount of data it pulls from NOAA. These are fed into a slew of Jinja for loops to generate the tables and pull the data to determine the curve points and controls for the temperature and precipitation ribbons.

### Notes

- Because this is using NOAA weather data, all units are in degrees Farenheight / miles / etc. If you use a different weather source or prefer a different measuring system you will need to adapt the code.
- The temperature ribbon bottoms out at 0 (well, 1 so there is always _something_ showing) and maxes out at 100.
- The hourly temperature ribbon's color gradient is horizontal and is determined by that hour's temperature, so as the height increases the color changes from blue to green to red.
- The daily temperature ribbon's color gradient is vertical and is determined by the overall min and max temperatures.
- The precipitation likelihood numbers show icons based on the precipType attribute, and only display if the chance is > 0%.
- All color choices were made with the intent of working on a dark theme. The temperature ribbons work pretty nicely on a light theme, but the color of the precipitation ribbon may need to be changed for visibility if you prefer a lighter theme.

### Dependencies

- Home Assistant
  - [GPS-based device_tracker](https://www.home-assistant.io/integrations/device_tracker/) (for local coordinates)
  - [Pirate Weather](https://pirate-weather.apiable.io/) account and API key (HA integration not needed)
  - [Auto Entities](https://github.com/thomasloven/lovelace-auto-entities)
  - [Button Card](https://github.com/custom-cards/button-card)
  - [Card Mod](https://github.com/thomasloven/lovelace-card-mod)
  - [Layout Card](https://github.com/thomasloven/lovelace-layout-card)
- [Node-Red](https://github.com/hassio-addons/addon-node-red) (for search coordinates)
  - [node-red-node-google](https://flows.nodered.org/node/node-red-node-google)
