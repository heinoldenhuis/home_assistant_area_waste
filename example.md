## Example

Instructions how to implement the waste calender has shown in the example below:

![Areawaste example](https://github.com/heinoldenhuis/home_assistant_area_waste/blob/master/images/overview_afvalkalender.jpg)

Sensor examples (configuration.yaml)
``` YAML
sensor:
  #Configuration example for the areawaste integration.
  #This will create the sensors: waste_today, waste_tomorrow, waste_green, waste_plastic and waste_residual.
  - platform: areawaste
    postcode: 7811AP
    housenumber: 1
    resources:
      - today
      - tomorrow
      - green
      - plastic
      - grey
  #Template sensors for date today and formatted sensors (Local names/translations in Dutch).
  - platform: template
    sensors:
      date_today: 
        friendly_name: 'Datum'
        value_template: >
          {% set months = ["januari", "februari", "maart", "april", "mei", "juni", "juli", "augustus", "september", "oktober", "november", "december"] %}
          {% set weekdays = ["zondag", "maandag", "dinsdag", "woensdag", "donderdag", "vrijdag", "zaterdag"] %}
          {% set date = strptime(state_attr('sensor.waste_today', 'date'), '%Y-%m-%d') %}
          {% set month = months[date.strftime('%m') | int -1] %}
          {% set weekday = weekdays[date.strftime('%w') | int] %}
          {{ weekday + ' ' + date.strftime('%d') + ' ' + month + ' '+ date.strftime('%Y') }}
      waste_green_formatted:
        friendly_name: 'GFT afval'
        entity_picture_template: /local/waste/gft_icon.png
        value_template: >
          {% set months = ["jan", "feb", "mrt", "apr", "mei", "jun", "jul", "aug", "sep", "okt", "nov", "dec"] %}
          {% set weekdays = ["Zo", "Ma", "Di", "Wo", "Do", "Vr", "Za"] %}
          {% set date = strptime(states('sensor.waste_green'), '%Y-%m-%d') %}
          {% set month = months[date.strftime('%m') | int -1] %}
          {% set weekday = weekdays[date.strftime('%w') | int] %}
          {{ weekday + ' ' + date.strftime('%d') + ' ' + month }}
      waste_plastic_formatted:
        friendly_name: 'Plastic'
        entity_picture_template: /local/waste/pmd_icon.png
        value_template: >
          {% set months = ["jan", "feb", "mrt", "apr", "mei", "jun", "jul", "aug", "sep", "okt", "nov", "dec"] %}
          {% set weekdays = ["Zo", "Ma", "Di", "Wo", "Do", "Vr", "Za"] %}
          {% set date = strptime(states('sensor.waste_plastic'), '%Y-%m-%d') %}
          {% set month = months[date.strftime('%m') | int -1] %}
          {% set weekday = weekdays[date.strftime('%w') | int] %}
          {{ weekday + ' ' + date.strftime('%d') + ' ' + month }}
      waste_residual_formatted:
        friendly_name: 'Restafval'
        entity_picture_template: /local/waste/restafval_icon.png
        value_template: >
          {% set months = ["jan", "feb", "mrt", "apr", "mei", "jun", "jul", "aug", "sep", "okt", "nov", "dec"] %}
          {% set weekdays = ["Zo", "Ma", "Di", "Wo", "Do", "Vr", "Za"] %}
          {% set date = strptime(states('sensor.waste_residual'), '%Y-%m-%d') %}
          {% set month = months[date.strftime('%m') | int -1] %}
          {% set weekday = weekdays[date.strftime('%w') | int] %}
          {{ weekday + ' ' + date.strftime('%d') + ' ' + month }}
      waste_today_formatted:
        friendly_name: 'Vandaag'
        value_template: >
          {% if state_attr('sensor.waste_today', 'trash_type') == "GREEN" %}GFT afval{%
          elif state_attr('sensor.waste_today', 'trash_type') == "GREY" %}Restafval{%
          elif state_attr('sensor.waste_today', 'trash_type') == "PLASTIC" %}Plastic{%
          else %}Geen{% endif %}
      waste_tomorrow_formatted:
        friendly_name: 'Morgen'
        value_template: >
          {% if state_attr('sensor.waste_tomorrow', 'trash_type') == "GREEN" %}GFT afval{%
          elif state_attr('sensor.waste_tomorrow', 'trash_type') == "GREY" %}Restafval{%
          elif state_attr('sensor.waste_tomorrow', 'trash_type') == "PLASTIC" %}Plastic{%
          else %}Geen{% endif %}
```

Automation example (automations.yaml)
``` YAML
# Rule: Notification rule for tomorrow waste collection
- id: '<unique automation id waste collection'
  alias: Waste collection tomorrow
  initial_state: 'on'
  trigger:
    platform: time
    at: '17:30:00'
  condition:
    - condition: template
      value_template: >
        {{ states('sensor.waste_tomorrow_formatted') != 'Geen' }}
  action:
    - service: notify.mail_notify
      data_template:
        title: >
          {{ now().strftime('%d %B') }}: Afvalmelding morgen
        message: >
          {{ states('sensor.waste_tomorrow_formatted') }} wordt morgen opgehaald!
    - service: persistent_notification.create
      data_template:
        notification_id: >
          {{ states('sensor.waste_tomorrow') }}-trash-notification
        title: >
          {{ now().strftime('%d %B') }}: Afvalmelding morgen
        message: >
          {{ states('sensor.waste_tomorrow_formatted') }} wordt morgen opgehaald!
```

UI Lovelace example (ui-lovelace.yaml)
>Note: Using Lovelace Markdown Mod (HACS) and Lovelace Card Mod (HACS).
``` YAML
resources:
  - url: /community_plugin/lovelace-card-mod/card-mod.js
    type: module
  - url: /community_plugin/lovelace-markdown-mod/markdown-mod.js
    type: module
views:
  - title: <Your view>
    cards:
      - type: vertical-stack
        title: Afval
        cards:
          - type: markdown
            title: Afval kalender
            style: |
              ha-card {
                --ha-card-background: rgba(0, 0, 0, 0.0);
              }
            content: |
              Vandaag is het **<font color=#5294E2>[[ sensor.date_today.state ]]</font>**.
          - type: glance
            style: |
              ha-card {
                --ha-card-background: rgba(0, 0, 0, 0.0);
              }
            entities:
              - entity: sensor.waste_green_formatted
                name: GFT afval
              - entity: sensor.waste_plastic_formatted
                name: Plastic
              - entity: sensor.waste_residual_formatted
                name: Restafval
          - type: horizontal-stack
            cards:
              - type: picture-entity
                entity: sensor.waste_today_formatted
                name: Vandaag
                state_image:
                  'Geen': /local/waste/nowaste_image.png
                  'GFT afval': /local/waste/gft_image.png
                  'Plastic': /local/waste/pmd_image.png
                  'Restafval': /local/waste/restafval_image.png
              - type: picture-entity
                entity: sensor.waste_tomorrow_formatted
                name: Morgen
                state_image:
                  'Geen': /local/waste/nowaste_image.png
                  'GFT afval': /local/waste/gft_image.png
                  'Plastic': /local/waste/pmd_image.png
                  'Restafval': /local/waste/restafval_image.png
```

Pictures used in this example can be found [here](https://github.com/heinoldenhuis/home_assistant_area_waste/blob/master/images/waste).

You can place these images in your Home Assistant config folder 'config/www/waste'.
