## Description

The UPnP port mapping code crashes when it encounters certain home routers — specifically devices that respond correctly to UPnP discovery but only advertise service types that the client doesn't know how to use. Huawei home gateways are a known example: they respond to discovery with a valid device description, but all of their services use proprietary or DSL Forum namespaces rather than the standard internet gateway service types.

When a port mapping is attempted against such a device, the code does not account for the possibility that no usable service was found. Instead of gracefully skipping the device, it proceeds with an absent service reference and crashes.

## Expected Behavior

- When a discovered UPnP device has no supported services, the client selection step should return an empty result (no usable client) rather than crash.
- The port mapping attempt against such a device should return a clean failure — no mapping obtained, no panic.
- The network probe should still correctly report that a UPnP-capable device exists on the network, even if that device's services are not supported.
- No crash or panic should occur during any of these steps.

## Why This Matters

Users with Huawei (and potentially other vendor-specific) routers see Tailscale crash or fail unexpectedly during port mapping discovery. The fix should make the system degrade gracefully — detecting the device but skipping port mapping — so the rest of the application continues working normally.

See also: https://github.com/tailscale/tailscale/issues/10911
