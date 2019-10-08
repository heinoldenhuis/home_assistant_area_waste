# Home Assistant integration for bin/waste collection for Area Reiniging

The Area Waste integration will retrieve calendar data from Area Reiniging.

The component is supposed to work for Coevorden, Emmen and Hoogeveen.

>Note: For Coevorden and Emmen the Green, Plastic and Residual waste sensors are available.
Hoogeveen also has Remainder and Trees waste sensors available.

>Note: This integration is based upon the work from Floris Kruisselbrink: [Github](https://github.com/vloris/home-assistant/blob/master/custom_components/sensor/twentemilieu.py) (supports Twentemilieu (Enschede)).
Also an integration is made by Cadsters for ACV (Ede, Renkum, Renswoude, Veenendaal and Wageningen) which can be found at HACS and [Github](https://github.com/Cadsters/acv-hass-component).
Many thanks because little work was needed to support Area Reiniging.

## HACS (Home Assistant Community Store)

This is a custom component. Custom components are not installed by default in your Home Assistant installation.
[HACS](https://custom-components.github.io/hacs/) is an Home Assistant store integration from which this integration can be easily installed and updated.
By using HACS you will also make sure that any new versions are installed by default and as simple as the installation itself.

## Manual installation

Create a directory called `areawaste` in the `<config directory>/custom_components/` directory on your Home Assistant instance.
Install this component by copying the files in [`/custom_components/areawaste/`]
(https://raw.githubusercontent.com/heinoldenhuis/home_assistant_area_waste/master/custom_components/areawaste/__init__.py, 
https://raw.githubusercontent.com/heinoldenhuis/home_assistant_area_waste/master/custom_components/areawaste/manifest.json and  https://raw.githubusercontent.com/heinoldenhuis/home_assistant_area_waste/master/custom_components/areawaste/sensor.py) from this repo into the new `<config directory>/custom_components/areawaste/` directory you just created.

This is how your custom_components directory should be:
```bash
custom_components
├── areawaste
│   ├── __init__.py
│   ├── manifest.json
│   └── sensor.py
```

## Configuration example

To enable this sensor, add the following lines to your configuration.yaml file:

``` YAML
sensor:
  - platform: areawaste
    postcode: 7811AP
    housenumber: 1
    resources:
      - today
      - tomorrow
      - grey
      - green
      - plastic
```

Configuration variables:

* **postcode** (Required): A valid postcode (postal code) in the Area Reiniging area.
* **housenumber** (Required): A valid housenumber.
* **resources** (Required): List of resourced which will be presented as sensors:
  * *today*: Sensor which indicates which waste will be collected for today.
  * *tomorrow*: Sensor which indicates which waste will be collected for tomorrow.
  * *grey*: Sensor which indicated when the residual (Restafval) waste will be collected.
  * *paper*: Sensor which indicated when the paper (Papier) waste will be collected.
  * *green*: Sensor which indicated when the green (GFT-afval) waste will be collected.
  * *plastic*: Sensor which indicated when the plastic (PMD) waste will be collected.
  * *remainder*: Sensor which indicated when the remainder (Restgoedwagen) waste will be collected.
  * *textile*: Sensor which indicated when the textile (Textiel) waste will be collected.
  * *tree*: Sensor which indicated when the tree (Kerstbomen) waste will be collected.

## Example

![Areawaste example](https://github.com/heinoldenhuis/home_assistant_area_waste/blob/master/images/overview_afvalkalender.jpg)

Instructions how to implement the example can be found [here](https://github.com/heinoldenhuis/home_assistant_area_waste/blob/master/example.md).
