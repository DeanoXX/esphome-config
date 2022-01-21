# ESPHome Configs
Key elements of My ESPHome YAML Configs

## Sonoff NSPanel
I've used the nspanel component from the ESPHome team to drive my NSPanel. Its now possible to drive the display elements not fully supported by the components (Weather/Widgets/Thermostat) by writing JSON directly to the panel and receiving JSON messages in return via the on_json_message trigger.
My Yaml shows working examples of widgets for a Bulb (Supporting Dim), RGB LED Strip (Dim+RGB), Single socket and a Group of 2 Sockets and the Thermostat. All widgets and thermostat are fully 2-way sync'd with Home Assistant
It uses the wttr.in service to retrieve Weather Information

The JSON protocol has been reverse engineered and docuements by Blakadder [here](https://blakadder.github.io/nspanel).

The nspanel component was created by [jesserockz](https://github.com/jesserockz) and amended to allow the direct 2-way JSON communication

Huge thanks to both !

Whilst in development the component can be included :-
```


Many Thanks to both !
