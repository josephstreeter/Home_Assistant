# Home Assistant Docker Compose Stack

This repository contains a comprehensive Home Assistant smart home setup using Docker Compose. It orchestrates three main services: Home Assistant, Mosquitto (MQTT broker), and Zigbee2MQTT, along with complete automation dashboards, sensor monitoring, and CI/CD deployment workflows. The configuration is designed for reliability, maintainability, and ease of use.

## 🚀 Features

- **Complete Home Assistant Stack**: Home Assistant, Mosquitto MQTT, and Zigbee2MQTT
- **Advanced Dashboard System**: System monitoring and automation control interfaces
- **Smart Automations**: Sunset/sunrise lighting with vacation and auto-light controls
- **Template Sensors**: Real-time monitoring of automation counts and system status
- **CI/CD Pipeline**: GitHub Actions workflow for automated deployments
- **Modular Configuration**: Organized YAML includes for maintainability
- **Weather Integration**: NWS weather alerts and monitoring

## Services

### 1. Home Assistant

- **Image:** `homeassistant/home-assistant:2024.6.2`
- **Purpose:** Central smart home automation platform. Integrates with various devices and protocols.
- **Volumes:**
  - `ha_data:/config/` (named volume for all runtime data and unmanaged files)
  - `./homeassistant/configuration.yaml:/config/configuration.yaml` (bind mount for main config)
  - `./homeassistant/includes:/config/includes` (bind mount for includes directory)
  - `/etc/localtime:/etc/localtime:ro`
- **Network Mode:** Host (for device discovery and integrations).
- **Environment:**
  - `TZ`: Sets the time zone.
- **Healthcheck:** Monitors service health via HTTP.
- **Labels:** Metadata for management and automation.
- **Logging:** Rotates logs for diagnostics.

### 2. Mosquitto (MQTT Broker)

- **Image:** `eclipse-mosquitto:2.0.18`
- **Purpose:** MQTT message broker for IoT devices and Zigbee2MQTT integration.
- **Ports:**
  - `1883`: MQTT protocol.
  - `9001`: WebSocket support.
- **Volumes:**
  - `./mosquitto.conf:/mosquitto/config/mosquitto.conf` (bind mount for main config)
  - `mosquitto_data:/mosquitto/data` (named volume for persistent data)
  - `mosquitto_log:/mosquitto/log` (named volume for logs)
- **Environment:**
  - `TZ`: Sets the time zone.
- **Labels:** Metadata for management and automation.
- **Logging:** Rotates logs for diagnostics.

### 3. Zigbee2MQTT

- **Image:** `koenkk/zigbee2mqtt:1.33.1`
- **Purpose:** Bridges Zigbee devices to MQTT, enabling integration with Home Assistant.
- **Ports:**
  - `8080`: Zigbee2MQTT web interface.
- **Volumes:**
  - `./zigbee2mqtt/configuration.yaml:/app/data/configuration.yaml` (bind mount for Zigbee2MQTT config)
  - `zigbee2mqtt_data:/app/data` (named volume for Zigbee2MQTT runtime data)
  - `/run/udev:/run/udev:ro`
- **Devices:**
  - `/dev/ttyUSB0`: Zigbee USB adapter.
- **Depends On:** Mosquitto (ensures MQTT broker is available).
- **Labels:** Metadata for management and automation.
- **Logging:** Rotates logs for diagnostics.

## Configuration Structure

### Home Assistant Configuration

- **Main Config**: `homeassistant/configuration.yaml` - Core Home Assistant settings
- **Dashboard Configurations**:
  - `includes/dashboards/system-monitor.yaml` - System health and resource monitoring
  - `includes/dashboards/automation-control.yaml` - Automation management interface
- **Component Configurations**:
  - `includes/automations.yaml` - Smart home automations
  - `includes/sensors.yaml` - Template sensors for monitoring
  - `includes/scenes.yaml` - Lighting and device scenes
  - `includes/scripts.yaml` - Reusable automation scripts
  - `includes/input_booleans.yaml` - Toggle switches for automation control

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
- `mosquitto_data` (named volume for Mosquitto persistent data)
- `mosquitto_log` (named volume for Mosquitto logs)
- `./mosquitto/mosquitto.conf` (bind mount for Mosquitto config)
- `zigbee2mqtt_data` (named volume for Zigbee2MQTT runtime data)
- `./zigbee2mqtt/configuration.yaml` (bind mount for Zigbee2MQTT config)

## Secrets

  A template for secrets is included (commented out). Use this for sensitive data such as MQTT passwords.

## Deployment

### GitHub Actions CI/CD

This repository includes a GitHub Actions workflow (`.github/workflows/deploy.yml`) for automated deployment:

- **Trigger**: Automatic deployment on pushes to the main branch
- **Runner**: Self-hosted runner with Docker access
- **Process**: Copies configuration files and restarts the Docker stack
- **Verification**: Checks deployment status after completion

### Manual Deployment

1. Ensure Docker and Docker Compose are installed.
2. Place your configuration files in the appropriate folders (see volume mappings).
3. Start the stack:

   ```bash
   docker-compose up -d
   ```

4. Access Home Assistant via `http://localhost:8123`.
5. Access Zigbee2MQTT web interface via `http://localhost:8080`.

## Dashboard Access

- **Home Assistant Main UI**: `http://localhost:8123`
- **System Monitor Dashboard**: Navigate to "System Monitor" in the sidebar
- **Automation Control**: Navigate to "Automation Control" in the sidebar
- **Setup Instructions**: Navigate to "Setup Guide" in the sidebar for integration setup
- **Zigbee2MQTT Interface**: `http://localhost:8080`

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
├── homeassistant/
│   ├── configuration.yaml         # Main Home Assistant config
│   └── includes/
│       ├── automations.yaml       # Smart home automations
│       ├── sensors.yaml           # Template sensors
│       ├── scenes.yaml            # Lighting scenes
│       ├── scripts.yaml           # Automation scripts
│       ├── input_booleans.yaml    # Control switches
│       └── dashboards/
│           ├── system-monitor.yaml        # System monitoring dashboard
│           ├── automation-control.yaml    # Automation control interface
│           ├── setup-instructions.yaml    # Integration setup guide
│           └── helpers.yaml               # Helper entity management
├── mosquitto/
│   └── mosquitto.conf             # MQTT broker configuration
└── zigbee2mqtt/
    └── configuration.yaml          # Zigbee2MQTT settings
```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test with your Home Assistant instance
5. Submit a pull request

## Support

- **Home Assistant Documentation**: [home-assistant.io](https://www.home-assistant.io/)
- **Zigbee2MQTT Documentation**: [zigbee2mqtt.io](https://www.zigbee2mqtt.io/)
- **Mosquitto Documentation**: [mosquitto.org](https://mosquitto.org/)

---

**Last Updated:** August 18, 2025
