# Overview
Home Assistant blueprint of domain script to turn off all devices in a selected Home Assistant area contains the following features:
- Exclude entities that should not be turned off (e.g. security systems, refrigerators).
- Specify primary domains to target (e.g. switch, light, fan, media_player).
- Additionally climate devices are set to 'off' mode and covers are closed.
- Retry logic included to ensure devices are turned off.
- Final notification lists any devices that remain on after attempts to turn them off.

# Input Variables
- area: the name of the area to turn off all entities.
- primary_domains: a comma-separated list of primary domains (default: 'switch,light,fan,media_player').    
- exclude_entities: an array of entity IDs to ignore.
- retry_count: number of times to retry turning off devices if they fail.
- retry_delay: delay in seconds between retry loops.
- on_states: a comma-separated list of states that indicate a device is "on" (default: 'on,playing,home,open')

# Sample Script using this blueprint


# Excluded Entities
Run the following template in Home Assistant -> Developer Tools -> Template to help determine what entities require excluding.  
The entities that you want to exclude from being turned off in an area are then added to the exclude_entities input variable

``` yaml
{% set input_area = 'Lounge' %}
{% set input_exclude_entities = [ ] %}
{% set input_primary_domains = 'switch,light,fan,media_player' %}

primary_entities:
  {{ 
    expand(area_entities(input_area)) |
      rejectattr('entity_id','in',input_exclude_entities) | 
      selectattr('domain','in',input_primary_domains.split(',') | map('trim') | list) | 
      map(attribute='entity_id') | 
      list 
  }}

climate_entities:
  {{ 
    expand(area_entities(input_area)) |
      selectattr('domain','equalto','climate') | 
      map(attribute='entity_id') | 
      list 
  }}

cover_entities:
  {{ 
    expand(area_entities(input_area)) |
      selectattr('domain','equalto','cover') | 
      map(attribute='entity_id') | 
      list 
  }}
```

# Changelog
