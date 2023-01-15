# Alternative Nabu Casa haaska alexa

With this "solution" it is possible to command HomeAssistant from Alexa without having to EXPRESS ENTITY and without using Nabo or haaska and AWS.

**Requirement**
- Customized <i>[alexa media player](https://github.com/custom-components/alexa_media_player)</i> 

**Installation**
Create a group with all entities media_player alexa
```
group:
  echos: 
    entities:
      - media_player.camera_2
      - media_player.studio
```
Add sensor to detect the last sentence spoken
```
sensor:
  - platform: template
    sensors:
      last_called_summary: 
        value_template: >
              {% set media = expand(states.group.echos)|selectattr('attributes.last_called','eq',true)|map(attribute='entity_id')|first%}
              {{ state_attr(media,'last_called_summary') }}
```
Use the sensor as an automation trigger. 
```
automation:
  - alias: Alexa 
    id: alexa_new
    initial_state: true
    mode: single
    max_exceeded: silent
    trigger:
    - platform: state
      entity_id: sensor.last_called_summary
      to: 
        - 'spegni luce studio' 
        - 'alexa spegni luce studio'
    action:
    - service: switch.turn_off
      target:
        entity_id: switch.luce_studio
```
To avoid getting "error" messages from alexa, **create a routine** in the **Alexa** mobile application with (when this happens) the phrase used in the trigger, and as an action the response you want to get
