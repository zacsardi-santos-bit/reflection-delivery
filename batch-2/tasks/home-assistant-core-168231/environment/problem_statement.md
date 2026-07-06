## Description

The Duco ventilation integration does not currently support the Home Assistant diagnostics feature. When users or developers need to troubleshoot issues with a Duco ventilation device, they cannot use the standard Home Assistant diagnostics download to collect and share device information. Adding diagnostics support would allow users to export a structured report covering all the relevant device and network data.

## Expected Behavior

- The integration should expose a diagnostics endpoint that returns a structured report containing:
  - Board hardware information (device name, subtype)
  - Network/LAN configuration (IP address, gateway, DNS, signal strength, network mode)
  - Connected node details (node IDs, general info, sensor readings, ventilation state)
  - Ventilation system component diagnostic status (component name and health status)
  - The number of remaining write requests available to the device
- Sensitive identifying information must be automatically masked in the report, including serial numbers, MAC addresses, hostnames, and the device IP/host configured in the integration entry

## Why This Matters

Without diagnostics support, users experiencing problems with their Duco ventilation system have no easy way to provide support teams with a clean, structured dump of their device state. Adding this capability aligns the integration with Home Assistant quality standards and makes troubleshooting significantly easier while protecting user privacy.
