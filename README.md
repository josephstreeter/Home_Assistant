# Home Assistant Docker Compose Stack

This repository contains a comprehensive Home Assistant smart home setup using Docker Compose. It provides a streamlined Home Assistant installation with ZHA (Zigbee Home Automation) integration, complete automation dashboards, sensor monitoring, and CI/CD deployment workflows. The configuration is designed for reliability, maintainability, and ease of use.

## 🚀 Features

- **Home Assistant with ZHA**: Direct Zigbee device integration using ZHA
- **Advanced Dashboard System**: System monitoring and automation control interfaces
- **Smart Automations**: Sunset/sunrise lighting with vacation and auto-light controls
- **Template Sensors**: Real-time monitoring of automation counts and system status
- **CI/CD Pipeline**: GitHub Actions workflow for automated deployments
- **Modular Configuration**: Organized YAML includes for maintainability
- **Weather Integration**: NWS weather alerts and monitoring

## Services

### 1. Home Assistant

- **Image:** `homeassistant/home-assistant:2025.1.0`
- **Purpose:** Central smart home automation platform with built-in ZHA for Zigbee device integration.
- **Volumes:**
  - `ha_data:/config/` (named volume for all runtime data and unmanaged files)
  - `./homeassistant/configuration.yaml:/config/configuration.yaml` (bind mount for main config)
  - `./homeassistant/includes:/config/includes` (bind mount for includes directory)
  - `/etc/localtime:/etc/localtime:ro`
- **Network Mode:** Host (for device discovery and integrations).
- **Devices:**
  - `/dev/ttyUSB0:/dev/ttyUSB0` (Zigbee USB adapter for ZHA integration)
- **Environment:**
  - `TZ`: Sets the time zone.
- **Healthcheck:** Monitors service health via HTTP.
- **Labels:** Metadata for management and automation.
- **Logging:** Rotates logs for diagnostics.

## Zigbee Integration

This setup uses **ZHA (Zigbee Home Automation)** integration built into Home Assistant for direct Zigbee device management:

- **Direct Integration**: No additional containers required for Zigbee support
- **USB Device**: Zigbee coordinator directly connected to Home Assistant container
- **Device Support**: Wide compatibility with Zigbee 3.0 devices
- **Management**: Built-in ZHA interface for device pairing and management

### Migrated from Zigbee2MQTT + Mosquitto

This configuration has been simplified by migrating from the previous Zigbee2MQTT + Mosquitto setup to the native ZHA integration, reducing complexity and container overhead while maintaining full Zigbee functionality.

## Configuration Structure

### Home Assistant Configuration

- **Main Config**: `homeassistant/configuration.yaml` - Core Home Assistant settings
- **Dashboard Configurations**:
  - `includes/dashboards/system-monitor.yaml` - System health and resource monitoring
  - `includes/dashboards/automation-control.yaml` - Automation management interface
- **Component Configurations**: All include files contain descriptions and commented examples for easy customization

#### Core Component Files

- **`includes/automations.yaml`** - Home Assistant automation entities
  - Rules that automatically trigger actions based on conditions and triggers
  - Consists of triggers (what starts), conditions (optional checks), and actions (what happens)
  - Example: Motion-activated lighting with illuminance checks

- **`includes/sensors.yaml`** - Home Assistant sensor entities
  - Template sensors, REST sensors, and platform-specific sensors
  - Provides data from weather APIs, system stats, and calculated values
  - Example: Temperature sensors with device class and unit configuration

- **`includes/binary_sensors.yaml`** - Home Assistant binary sensor entities
  - Two-state sensors (on/off, true/false) for motion, doors, windows, and alerts
  - MQTT, template, and platform-specific binary sensors
  - Examples: Door sensors, temperature alerts with availability templates

- **`includes/scenes.yaml`** - Home Assistant scene entities
  - Snapshots of device states for coordinated device control
  - Useful for lighting moods, security settings, and device orchestration
  - Examples: Evening lighting, morning brightness, movie mode scenes

- **`includes/scripts.yaml`** - Home Assistant script entities
  - Reusable sequences of actions for common tasks
  - Can include service calls, delays, conditions, and complex logic
  - Examples: Startup routines, bedtime sequences, system monitoring scripts

#### Helper Entity Files

- **`includes/input_booleans.yaml`** - Input boolean helpers
  - Toggleable switches for manual control and automation conditions
  - Used in dashboards, automations, and mode switching
  - Examples: Vacation mode, guest mode, automation enable/disable switches

- **`includes/input_numbers.yaml`** - Input number helpers  
  - Adjustable sliders and number inputs for user-configurable parameters
  - Used for thresholds, delays, and numeric automation values
  - Examples: Temperature thresholds, motion timeouts, lighting offsets

- **`includes/input_select.yaml`** - Input select helpers
  - Dropdown lists with predefined options for user interfaces
  - Useful for mode selection and state management
  - Examples: House modes (Home/Away/Sleep), thermostat presets

#### Device Control Files

- **`includes/lights.yaml`** - Home Assistant light entities
  - Smart bulbs, LED strips, dimmers from various integrations
  - MQTT, Zigbee, Z-Wave, and template light configurations
  - Examples: MQTT lights with brightness, color temperature, and availability

- **`includes/switches.yaml`** - Home Assistant switch entities
  - Smart plugs, relays, and other controllable devices
  - MQTT, template, and platform-specific switch configurations  
  - Examples: MQTT switches with state/command topics and availability

#### Organization Files

- **`includes/groups.yaml`** - Home Assistant group entities
  - Organize related entities for easier management and automation
  - Groups lights, sensors, switches, and other logical device collections
  - Examples: Room lighting groups, sensor collections, device categories

- **`includes/templates.yaml`** - Home Assistant template sensors and binary sensors
  - Jinja2 template-based entities for calculated states and attributes
  - Weather alerts, system monitoring, and derived sensor values
  - Examples: Weather alert binary sensors, temperature calculations

- **`includes/dashboard.yaml`** - Home Assistant dashboard configurations
  - Custom dashboard YAML syntax for user interfaces
  - Multiple views, card layouts, and interface customization
  - Examples: Multi-view dashboards with entity cards, weather, and gauges

### Automation Features

- **Evening Sunset Lighting**: Automatically turns on lights 15 minutes before sunset
- **Morning Sunrise Lights Off**: Turns off lights 30 minutes after sunrise
- **Smart Controls**: Vacation mode and auto-lights toggles for manual override
- **Test Automation**: Sample automation for testing notification systems

### Dashboard Features

- **System Monitor Dashboard**:
  - System resource monitoring (CPU, Memory, Disk)
  - Home Assistant health metrics
  - Docker container status
  - Weather alerts and notifications
- **Automation Control Dashboard**:
  - Multi-tab interface for automation overview, scene control, script control, and system settings
  - **Automation Overview**: Manage automations, view statistics, and recent activity
  - **Helper Controls**: Vacation, guest, sleep, auto lights, security, and environment toggles and settings
  - **Lighting Controls**: Adjust sunrise/sunset offsets, auto lights, and related settings
  - **Security Controls**: Motion lights, door/window alerts, timeouts, and monitoring toggles
  - **Environment Controls**: Climate automation, temperature/humidity thresholds, weather alerts
  - **Scene Control**: Activate and manage scenes (evening, morning, movie, party)
  - **Script Control**: Run and monitor scripts (startup, bedtime, away, welcome home)
  - **Quick Actions**: Horizontal stacks of buttons for fast scene/script activation
  - **Activity Logs**: Logbook cards for recent automation and script activity
  - **Statistics**: Automation counters and status sensors
- **Setup Instructions Dashboard**:
  - Complete setup guide for System Monitor integration
  - Step-by-step instructions for creating required helper entities
  - Entity configuration checklist for automation dashboard functionality
  - Tips and troubleshooting for proper entity management

### Template Sensors

- **Automation Counters**: Total, enabled, and disabled automation counts
- **System Metrics**: Script count, sensor count, and system health
- **Weather Alerts**: NWS weather alert integration with zone monitoring

## Volumes

- `ha_data` (named volume for Home Assistant runtime data)
- `./homeassistant/configuration.yaml` (bind mount for Home Assistant main config)
- `./homeassistant/includes` (bind mount for Home Assistant includes directory)

## Deployment

### GitHub Actions CI/CD

This repository includes a GitHub Actions workflow (`.github/workflows/deploy.yml`) for automated deployment:

- **Trigger**: Automatic deployment on pushes to the main branch
- **Runner**: Self-hosted runner with Docker access
- **Process**: Copies configuration files and restarts the Docker stack
- **Verification**: Checks deployment status after completion

#### Workflow Details

The deployment workflow performs the following steps:

1. **Repository Checkout**: Uses `actions/checkout@v4` to pull the latest code from the main branch
2. **File Synchronization**: Copies updated files from the GitHub workspace to the Docker host:
   - `docker-compose.yml` - Main Docker Compose configuration
   - `homeassistant/configuration.yaml` - Home Assistant main configuration
   - `homeassistant/includes/` - Complete includes directory with all modular configurations
   - `mosquitto/mosquitto.conf` - Mosquitto MQTT broker configuration (legacy)
3. **Stack Deployment**: Executes `docker-compose up -d` to update and restart containers with zero-downtime deployment
4. **Verification**: Runs `docker-compose ps` to verify all services are running correctly

#### Prerequisites

- **Self-hosted Runner**: Configured with access to the target Docker host
- **Directory Structure**: Runner must have access to `/home/docker/homeassistant/` deployment directory
- **Docker Access**: Runner user must have Docker permissions for container management
- **Network Access**: Runner needs connectivity to Docker daemon and container registry

### Manual Deployment

1. Ensure Docker and Docker Compose are installed.
2. Place your configuration files in the appropriate folders (see volume mappings).
3. Start the stack:

   ```bash
   docker-compose up -d
   ```

4. Access Home Assistant via `http://localhost:8123`.
5. Configure ZHA integration through Home Assistant UI: Settings → Devices & Services → Add Integration → ZHA.

## Dashboard Access

- **Home Assistant Main UI**: `http://localhost:8123`
- **System Monitor Dashboard**: Navigate to "System Monitor" in the sidebar
- **Automation Control**: Navigate to "Automation Control" in the sidebar
- **Setup Instructions**: Navigate to "Setup Guide" in the sidebar for integration setup
- **ZHA Integration**: Access through Settings → Devices & Services → ZHA for Zigbee device management

## Automation Management

### Available Automations

1. **Test Sample Automation**: Sends notifications every 30 minutes for testing
2. **Evening Sunset Lighting**: Activates evening lights before sunset
3. **Morning Sunrise Lights Off**: Turns off lights after sunrise

### Control Switches

- **Auto Lights**: Enable/disable automatic lighting controls
- **Vacation Mode**: Override all automations when away
- **Guest Mode**: Modify behavior for guests
- **Sleep Mode**: Night-time automation adjustments

## Customization

- Adjust resource limits, environment variables, and volume paths as needed.
- Uncomment and configure secrets for secure deployments.
- Add or modify labels for integration with monitoring or orchestration tools.
- Customize dashboard layouts by editing YAML files in `includes/dashboards/`.
- Add new automations to `includes/automations.yaml`.
- Modify template sensors in `includes/sensors.yaml` for additional monitoring.

## Troubleshooting

### Common Issues

- Check container logs for errors:

    ```bash
    docker-compose logs <service>
    ```

- Ensure USB devices are accessible and mapped correctly.
- Verify network mode and port mappings if accessing services remotely.
- Check Home Assistant logs via the web interface: Settings → System → Logs.

### Automation Debugging

- Use the **Automation Control Dashboard** to monitor automation states
- Check template sensors for accurate counts and system metrics
- Verify input boolean states affect automation conditions correctly
- Test automations using the Home Assistant automation editor

### Dashboard Issues

- Ensure all referenced entities exist in Home Assistant
- Check YAML syntax in dashboard configuration files
- Verify template sensors are updating correctly
- Restart Home Assistant after dashboard configuration changes

## Repository Structure

```plaintext
├── .github/workflows/
│   └── deploy.yml                 # GitHub Actions deployment workflow
├── docker-compose.yml             # Main Docker Compose configuration
└── homeassistant/
    ├── configuration.yaml         # Main Home Assistant config
    ├── blueprints/
    │   └── script/
    │       └── confirmable_notification.yaml  # Actionable notification blueprint
    └── includes/
        ├── automations.yaml       # Automation entities with triggers/conditions/actions
        ├── binary_sensors.yaml    # Two-state sensors (motion, doors, alerts)
        ├── dashboard.yaml         # Custom dashboard YAML configurations
        ├── groups.yaml            # Entity organization and logical groupings
        ├── input_booleans.yaml    # Toggle switches for automation control
        ├── input_numbers.yaml     # Adjustable sliders and numeric inputs
        ├── input_select.yaml      # Dropdown lists with predefined options
        ├── lights.yaml            # Light entities (bulbs, strips, dimmers)
        ├── scenes.yaml            # Device state snapshots for coordinated control
        ├── scripts.yaml           # Reusable action sequences and routines
        ├── sensors.yaml           # Template and platform sensors for monitoring
        ├── switches.yaml          # Switch entities (plugs, relays, devices)
        ├── templates.yaml         # Jinja2 template sensors and binary sensors
        └── dashboards/
            ├── system-monitor.yaml        # System monitoring dashboard
            ├── automation-control.yaml    # Automation control interface
            ├── setup-instructions.yaml    # Integration setup guide
            └── helpers.yaml               # Helper entity management
```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test with your Home Assistant instance
5. Submit a pull request

## Support

- **Home Assistant Documentation**: [home-assistant.io](https://www.home-assistant.io/)
- **ZHA Documentation**: [ZHA Integration Guide](https://www.home-assistant.io/integrations/zha/)

---

**Last Updated:** August 28, 2025
