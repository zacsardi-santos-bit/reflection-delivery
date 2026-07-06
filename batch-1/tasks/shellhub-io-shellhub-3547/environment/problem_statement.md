## Description

The device listing feature has a few problems around how it determines whether a pending device can be accepted by a namespace operator.

Currently, when listing pending devices in the community self-hosted mode, the system unnecessarily queries how many devices were previously removed — a piece of information that is irrelevant for that deployment mode and adds an unnecessary database call. This call should only happen in the cloud-hosted deployment path.

More significantly, when devices are listed and the namespace still has capacity to accept more, the returned device objects are not being flagged as "acceptable" for non-accepted devices. This means operators cannot distinguish which pending devices they are actually allowed to accept — the acceptability field is always false even when the namespace has room.

There are also three distinct deployment modes (cloud-hosted, enterprise self-hosted, and community self-hosted) that should each have their own clearly separated logic for determining acceptability: cloud mode needs to account for historically removed devices when checking limits, enterprise and community modes work differently from each other, and all modes should derive the acceptability mode from the namespace's current device count versus its configured limit.

## Expected Behavior

- When listing devices under a namespace that has capacity, pending devices should be marked as eligible for acceptance.
- In community mode, the system should not make extra database queries to count previously removed devices — the acceptability decision should be based solely on the namespace's current device count versus its limit.
- Cloud mode, enterprise mode, and community mode should each follow their own logic for which device-acceptability rule to apply when calling the data layer.
- If a namespace has no device limit (limit set to -1), all non-accepted devices should be marked as eligible for acceptance.
- If a namespace has reached its device limit, devices should not be marked as acceptable.

## Why This Matters

Operators rely on the "acceptable" flag to know which devices they can act on from the pending queue. When this flag is incorrectly set to false even for namespaces with available capacity, operators cannot accept new devices through the UI or API without additional workarounds. The fix makes the device listing trustworthy and correctly separated by deployment context.
