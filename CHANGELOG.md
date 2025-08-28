# Changelog

All notable changes to this Home Assistant Docker Compose project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [3.0.0] - 2025-08-28

### Changed - BREAKING CHANGES

- **BREAKING**: Migrated from Zigbee2MQTT + Mosquitto to native ZHA integration
- **BREAKING**: Removed Mosquitto MQTT broker container
- **BREAKING**: Removed Zigbee2MQTT container
- **BREAKING**: Simplified docker-compose.yml to single Home Assistant service
- Updated Home Assistant to version 2025.1.0
- Direct USB device connection to Home Assistant container for ZHA

### Removed

- `mosquitto` service and related volumes (`mosquitto_data`, `mosquitto_log`)
- `zigbee2mqtt` service and related volumes (`zigbee2mqtt_data`)
- Mosquitto configuration files and directory structure
- Zigbee2MQTT configuration files and directory structure
- MQTT-related secrets and dependencies

### Added

- Native ZHA (Zigbee Home Automation) integration documentation
- Migration guide from Zigbee2MQTT to ZHA in README

### Fixed

- Simplified architecture reduces container overhead and complexity
- Improved reliability with native Home Assistant Zigbee support

## [2.1.0] - 2025-08-18

### Added

- Setup Instructions Dashboard (`setup-instructions.yaml`)
- Complete integration setup guide with System Monitor configuration
- Helper entity creation instructions
- Entity configuration checklist for automation dashboard
- Tips and troubleshooting for entity management

### Changed

- Enhanced README documentation with setup instructions dashboard
- Updated dashboard access section with setup guide navigation
- Improved repository structure documentation

## [2.0.0] - 2025-08-17

### Added - Core Features

- **System Monitor Dashboard** (`system-monitor.yaml`)
  - Multi-tab interface (System Overview, HA Health, Docker Containers, Alerts)
  - CPU, Memory, Disk usage monitoring with gauges and history
  - Home Assistant health metrics and entity counts
  - Docker container status monitoring
  - Weather alerts and system notifications
- **Automation Control Dashboard** (`automation-control.yaml`)
  - Multi-tab interface (Automation Overview, Scene Control, Script Control)
  - Automation management with statistics and activity logs
  - Helper controls (vacation, guest, sleep modes)
  - Lighting, security, and environment controls
  - Scene and script quick actions with horizontal button stacks
  - Input boolean and number controls for automation parameters
- **Template Sensors** (`sensors.yaml`)
  - Automation count sensors (total, enabled, disabled)
  - System metric sensors (script count, sensor count)
  - NWS weather alert integration with zone monitoring
- **Smart Automations** (`automations.yaml`)
  - Evening sunset lighting automation (15 minutes before sunset)
  - Morning sunrise lights off automation (30 minutes after sunrise)
  - Test automation with persistent notifications
  - Vacation mode and auto-lights condition support
- **Home Assistant Blueprints**
  - Automation blueprints for sunrise/sunset lighting
  - Script blueprints for confirmable notifications
  - Motion light automation blueprint
  - Zone notification blueprint

### Changed

- Enhanced docker-compose.yml with proper resource limits and logging
- Updated Home Assistant to version 2024.6.2
- Improved configuration structure with modular includes
- Enhanced README with comprehensive feature documentation

### Added - CI/CD

- **GitHub Actions Deployment Workflow** (`.github/workflows/deploy.yml`)
  - Self-hosted runner support
  - Automatic deployment on main branch pushes
  - Configuration file copying and container restart
  - Deployment verification

## [1.2.0] - 2025-08-17

### Added - Configuration Structure

- Modular configuration structure with includes directory
- Organized YAML configuration files:
  - `automations.yaml` - Automation definitions
  - `sensors.yaml` - Template and monitoring sensors
  - `scenes.yaml` - Lighting and environment scenes
  - `scripts.yaml` - Reusable automation scripts
  - `input_booleans.yaml` - Control switches
  - `groups.yaml` - Entity groupings
  - `secrets.yaml` - Sensitive configuration template

### Changed - Configuration

- Improved Home Assistant configuration structure
- Enhanced maintainability with separated configuration files

## [1.1.0] - 2025-08-16

### Added - Service Enhancements

- Health checks for all services (Home Assistant, Mosquitto, Zigbee2MQTT)
- Proper logging configuration with rotation
- Service labels for management and automation
- Resource limits and restart policies

### Changed - Docker Configuration

- Enhanced docker-compose.yml with production-ready configurations
- Improved service dependencies and startup order
- Updated volume mount strategy with bind mounts for configuration files

### Fixed - Stability

- Container restart issues with proper dependency management
- Volume permissions and mounting issues

## [1.0.0] - 2025-08-15

### Added - Initial Release

- Initial Home Assistant Docker Compose stack
- **Home Assistant** service (homeassistant/home-assistant:2024.6.2)
  - Host networking for device discovery
  - Configuration bind mounts
  - Named volume for runtime data
- **Mosquitto MQTT Broker** service (eclipse-mosquitto:2.0.18)
  - MQTT protocol support (port 1883)
  - WebSocket support (port 9001)
  - Persistent data and log volumes
- **Zigbee2MQTT** service (koenkk/zigbee2mqtt:1.33.1)
  - Zigbee device bridge to MQTT
  - Web interface (port 8080)
  - USB device mapping for Zigbee coordinator
- Docker volumes for data persistence
- Basic configuration structure
- Initial README documentation

### Infrastructure - Foundation

- Docker Compose version 3.8 configuration
- Proper service networking and dependencies
- USB device mapping for Zigbee coordinator
- Time zone configuration across all services

---

## Legend

- **Added** for new features
- **Changed** for changes in existing functionality
- **Deprecated** for soon-to-be removed features
- **Removed** for now removed features
- **Fixed** for any bug fixes
- **Security** in case of vulnerabilities
- **BREAKING** for breaking changes that require user action
