# ESPHome Configs
Key elements of My ESPHome YAML Configs

## Sonoff NSPanel
I've used the nspanel component from the ESPHome team to drive my NSPanel. Its now possible to drive the display elements not fully supported by the components (Weather/Widgets/Thermostat) by writing JSON directly to the panel and receiving JSON messages in return via the on_json_message trigger.
My Yaml shows working examples of widgets for a Bulb (Supporting Dim), RGB LED Strip (Dim+RGB), Single socket, a Group of 2 Sockets, a Cover (curtain) and the Thermostat. All widgets and thermostat are fully 2-way sync'd with Home Assistant
It uses the wttr.in service to retrieve Weather Information

The JSON protocol has been reverse engineered and docuements by Blakadder [here](https://blakadder.github.io/nspanel).

The nspanel component was created by [jesserockz](https://github.com/jesserockz) and amended to allow the direct 2-way JSON communication

Huge thanks to both !

Whilst in development the component can be included :-
```
external_components:
  - source: github://pr#2702
    components: ["nspanel"]
    refresh: 0s
```

With ESPHome version > 2022.1 you will be able to compile OK. If you're at a previous version you'll need the dev version of ESPHome.

I have a youtube demo of some of the working widgets and thermostat..https://youtu.be/7T5r5sy_rpc

### Defining Widgets

This YAML demonstates defining Widgets directly via JSON i.e. not within the nspanel YAML config.
Each Widget requires :-
 - Definition within script nspanel_init
   The script defines the widget ID (you must avoid using id of "1" to "8" to avoid conflicts with widgets created by the panel component itseld. the uiid defines the type of widget as per Blakadder's documentation. An initial state is also defined. If the Widget is not required the widget for that index (not id) must be declared as type "delete".
 - Code within the nspanel/on_json_message event to handle changes *from* the panel.
   Typically this would be automations to call services on HA itself or elsewhere within the ESPhome YAML. I've minimised the lambda to simply check the contents of the received JSON - trying to keep the subsequent automations within ESPHome YAML. The message type (integer "type") and JSON (JSON object "root") are available in the lambdas.
 - The necessary sensors (binary_sensor/sensor/text_sensor) to monitor the current states and key attributes from HA. The relevent triggers for changes then need to update the panel via lambda 'id(nspanel).send_json_command calls. Either directly or via more scripts. The JSON can either be built via text - or use the arduiono JSON library to build an object. The message are simple enough (imo) that use of the direct text method is more intuitive in the YAML
 
 The flow for updating the panel in respone to touch changes is:
 
  - State amended on panel by user
	- JSON sent to ESPhome via "on_json_message" event
	- ESPHome updates Home Assistant via service calls
	- ESPHome state sensors react to entity state updates from HA
	- State/Value change events send json to update panel
	
	i.e. any changes made on the panel dont "stick" unless the change is seen on HA and refelcted back to the panel within a second or so. If the full loop doesnt complete, the changes made on the panel revert to the previous state. If you simply amend objects on ESPhome you would still need to reflect changes back from the ESPHome relevant entities - but without the need for HA service calls / sensors.
	
	The thermostat operates very similair to widgets interacting with a climate entity on HA. In my example is a simple Heat thermostat. The range is limited by the panel 

### Defining Weather
	
The Weather is provided by the wttr.in JSON service mimicing the approach from Tasmota (and re-using blakadder's mapping of weather ICON between wttr.in and the available NSPanel states). note the vlaidation of the wttr.in https certificate has been disabled to simplify the request. If you need certificate validation on the ESP there are documented methods available.