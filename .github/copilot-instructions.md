# GitHub Copilot Repository Instructions

This document provides comprehensive instructions for using GitHub Copilot to effectively manage and contribute to this Home Assistant Docker Compose repository.

## Repository Overview

This repository contains a modular Home Assistant smart home setup using Docker Compose with ZHA (Zigbee Home Automation) integration. The configuration is designed for maintainability, reliability, and ease of use.

### Key Architecture Principles

- **Modular Configuration**: All Home Assistant configurations are split into logical files in the `includes/` directory
- **Self-Documenting**: Every YAML file contains descriptions and commented examples
- **Dynamic Groups**: Automated entity grouping using template sensors and startup automations
- **CI/CD Ready**: GitHub Actions workflow for automated deployment
- **ZHA Integration**: Native Zigbee support without additional containers

## File Structure and Organization

### Configuration Files (`homeassistant/includes/`)

When working with configuration files, follow these patterns:

#### Core Component Files

- `automations.yaml` - Automation rules with triggers, conditions, and actions
- `sensors.yaml` - Template sensors, REST sensors, and monitoring entities
- `binary_sensors.yaml` - Two-state sensors (motion, doors, windows, alerts)
- `scenes.yaml` - Device state snapshots for coordinated control
- `scripts.yaml` - Reusable action sequences and routines

#### Helper Entity Files

- `input_booleans.yaml` - Toggle switches for manual control
- `input_numbers.yaml` - Adjustable sliders and numeric inputs
- `input_select.yaml` - Dropdown lists with predefined options

#### Device Control Files

- `lights.yaml` - Light entities (bulbs, strips, dimmers)
- `switches.yaml` - Switch entities (plugs, relays, devices)

#### Organization Files

- `groups.yaml` - Static entity groupings
- `templates.yaml` - Jinja2 template sensors and binary sensors
- `dashboard.yaml` - Custom dashboard configurations
- `packages/dynamic_groups.yaml` - Self-maintaining dynamic groups

### Dashboard Files (`homeassistant/includes/dashboards/`)

- `system-monitor.yaml` - System health and resource monitoring
- `automation-control.yaml` - Automation management interface
- `setup-instructions.yaml` - Integration setup guide
- `helpers.yaml` - Helper entity management

## GitHub Copilot Best Practices

### 1. Context-Aware Configuration

When adding new entities, always:

```yaml
# Provide context comments for Copilot
# This sensor monitors living room temperature from Zigbee device
- platform: template
  sensors:
    living_room_temp_formatted:
      friendly_name: "Living Room Temperature"
      # Use proper device class for Home Assistant recognition
      device_class: temperature
      unit_of_measurement: "°F"
      # Template with error handling
      value_template: >
        {% if states('sensor.zigbee_temp_sensor') not in ['unknown', 'unavailable'] %}
          {{ (states('sensor.zigbee_temp_sensor') | float * 9/5 + 32) | round(1) }}
        {% else %}
          unavailable
        {% endif %}
```

### 2. Automation Development Patterns

Follow this structure for new automations:

```yaml
- id: unique_automation_id
  alias: 'Category: Descriptive Name'
  description: Clear description of what this automation does
  trigger:
    # Use specific trigger types with proper offsets
    - platform: sun
      event: sunset
      offset: -00:15:00
  condition:
    # Always check for mode overrides
    - condition: state
      entity_id: input_boolean.vacation_mode
      state: 'off'
    - condition: state
      entity_id: input_boolean.auto_lights
      state: 'on'
  action:
    # Use scenes for complex device coordination
    - service: scene.turn_on
      target:
        entity_id: scene.evening_lighting
    # Always provide user feedback
    - service: notify.persistent_notification
      data:
        title: "Automation Executed"
        message: "{{ trigger.platform }} automation completed at {{ now().strftime('%Y-%m-%d %H:%M:%S') }}"
  mode: single
```

### 3. Template Sensor Best Practices

When creating template sensors, use these patterns:

```yaml
- platform: template
  sensors:
    entity_name:
      friendly_name: "Human Readable Name"
      # Always include availability template
      availability_template: >
        {{ states('source.entity') not in ['unknown', 'unavailable'] }}
      # Use error handling in value templates
      value_template: >
        {% if states('source.entity') not in ['unknown', 'unavailable'] %}
          {{ states('source.entity') | float | round(2) }}
        {% else %}
          0
        {% endif %}
      # Include proper device class and units
      device_class: temperature
      unit_of_measurement: "°C"
      # Add useful attributes
      attribute_templates:
        source: "{{ states('source.entity') }}"
        last_updated: "{{ now().strftime('%Y-%m-%d %H:%M:%S') }}"
```

### 4. Dynamic Groups Integration

When adding new entity types, ensure they integrate with dynamic groups:

```yaml
# In packages/dynamic_groups.yaml, add new template sensor
- name: "All New Entity Type"
  unique_id: dynamic_new_entity_list
  state: >
    {{ 'on' if states.new_entity_type | selectattr('state', 'eq', 'on') | list | length > 0 else 'off' }}
  attributes:
    entities: >
      {{ states.new_entity_type | map(attribute='entity_id') | list }}

# Add corresponding automation
- id: "Update_dynamic_new_entity_group"
  alias: "Update dynamic new entity group"
  trigger:
    - platform: homeassistant
      event: start
  action:
    - service: group.set
      data:
        object_id: all_new_entities
        entities: >
          {{ state_attr('sensor.all_new_entity_type', 'entities') | join(', ') }}
```

### 5. Dashboard Development

When creating dashboard cards, follow these conventions:

```yaml
# Use consistent card structure
- type: entities
  title: "Section Title"
  show_header_toggle: true
  entities:
    - entity: input_boolean.example
      name: "Custom Name"
      icon: mdi:example-icon
    - type: divider
    - entity: sensor.example
      secondary_info: last-changed

# For gauge cards, include proper ranges
- type: gauge
  entity: sensor.cpu_percent
  min: 0
  max: 100
  severity:
    green: 0
    yellow: 60
    red: 80
  needle: true
```

## Development Workflow

### 1. Adding New Features

1. **Identify the correct file** based on entity type and purpose
2. **Add commented examples** if the file doesn't have them
3. **Test locally** before committing
4. **Update documentation** in README and CHANGELOG
5. **Ensure CI/CD compatibility** by checking workflow includes all necessary files

### 2. Configuration Validation

Before committing changes:

```bash
# Check Home Assistant configuration
docker-compose exec homeassistant python -m homeassistant --script check_config --config /config

# Validate YAML syntax
yamllint homeassistant/includes/*.yaml
```

### 3. Testing Approach

1. **Local Testing**: Use Docker Compose locally with test entities
2. **Staging Environment**: Test with actual devices in non-production environment
3. **Gradual Rollout**: Enable new automations gradually with input_boolean controls

### 4. Error Handling Patterns

Always implement proper error handling:

```yaml
# For templates
value_template: >
  {% if states('sensor.source') not in ['unknown', 'unavailable'] %}
    {{ states('sensor.source') | float | round(2) }}
  {% else %}
    {{ states('sensor.backup_source') | float | round(2) if states('sensor.backup_source') not in ['unknown', 'unavailable'] else 0 }}
  {% endif %}

# For automations
condition:
  - condition: template
    value_template: >
      {{ states('sensor.required_entity') not in ['unknown', 'unavailable'] }}
```

## Common Patterns and Anti-Patterns

### ✅ Do This

```yaml
# Use descriptive IDs and aliases
- id: evening_outdoor_security_lights
  alias: 'Security: Evening Outdoor Lights'
  
# Include proper condition checks
condition:
  - condition: state
    entity_id: input_boolean.vacation_mode
    state: 'off'
    
# Use scenes for complex device coordination
action:
  - service: scene.turn_on
    target:
      entity_id: scene.security_lighting
      
# Provide user feedback
  - service: notify.persistent_notification
    data:
      title: "Security Lights Activated"
      message: "Outdoor security lighting enabled at {{ now().strftime('%H:%M') }}"
```

### ❌ Avoid This

```yaml
# Generic or unclear naming
- id: automation_1
  alias: 'lights'
  
# Missing condition checks
# No vacation mode or override checks

# Individual device control instead of scenes
action:
  - service: light.turn_on
    entity_id: light.porch
  - service: light.turn_on
    entity_id: light.driveway
  - service: light.turn_on
    entity_id: light.backyard
    
# No user feedback or logging
```

## Troubleshooting Guide

### Common Issues and Solutions

1. **Configuration Errors**
   - Check YAML syntax with proper indentation
   - Validate entity IDs exist in Home Assistant
   - Ensure all required platforms are specified

2. **Template Errors**
   - Use Home Assistant template editor for testing
   - Include proper error handling and availability checks
   - Test with actual entity states

3. **Automation Not Triggering**
   - Verify trigger conditions are met
   - Check automation is enabled
   - Ensure condition statements don't block execution

4. **Dynamic Groups Not Updating**
   - Restart Home Assistant to trigger startup automations
   - Check template sensor states and attributes
   - Verify group.set service calls are working

## Integration with GitHub Actions

The repository includes automated deployment via GitHub Actions. When making changes:

1. **All changes in `includes/` directory are automatically deployed**
2. **Configuration validation happens in Home Assistant container**
3. **Deployment includes complete includes directory structure**
4. **Rollback capability through Git history**

### Deployment Workflow

The workflow automatically:

- Copies `docker-compose.yml`
- Copies `homeassistant/configuration.yaml`
- Copies entire `homeassistant/includes/` directory
- Restarts Docker stack with zero downtime
- Verifies deployment success

## Documentation Standards

### File Headers

Every include file should start with:

```yaml
# This file contains Home Assistant [entity_type] entities.
# [Description of purpose and usage]
# [Instructions for customization]
#
# Example [entity_type] configuration (commented out):
#
# [Commented example with all common properties]
```

### Inline Comments

Use descriptive comments for complex logic:

```yaml
# Check if it's nighttime and motion was detected in the last 5 minutes
condition:
  - condition: sun
    after: sunset
    after_offset: "-01:00:00"
  - condition: template
    value_template: >
      {{ (as_timestamp(now()) - as_timestamp(states.binary_sensor.motion.last_changed)) < 300 }}
```

### Change Documentation

Always update:

1. **CHANGELOG.md** with new features, changes, and fixes
2. **README.md** with new configuration options or setup requirements
3. **Inline documentation** in configuration files

## Security Considerations

1. **Never commit secrets** - use `secrets.yaml` for sensitive data
2. **Validate input** in templates and automations
3. **Use specific entity IDs** rather than wildcards where possible
4. **Implement rate limiting** for external API calls
5. **Regular updates** of Home Assistant and container images

---

## Quick Reference Commands

```bash
# Check configuration
docker-compose exec homeassistant python -m homeassistant --script check_config

# Restart Home Assistant
docker-compose restart homeassistant

# View logs
docker-compose logs -f homeassistant

# Validate YAML
yamllint homeassistant/includes/

# Deploy changes (automatic via GitHub Actions on push to main)
git add .
git commit -m "feat: add new automation for security lighting"
git push origin main
```

This guide ensures consistent, maintainable, and reliable Home Assistant configuration management using GitHub Copilot's capabilities.
