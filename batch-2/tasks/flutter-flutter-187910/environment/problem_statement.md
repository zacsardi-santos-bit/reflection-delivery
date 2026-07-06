## Description

Newer versions of Xcode ship with a new device management application alongside the traditional iOS Simulator. Flutter's tooling currently only knows how to locate the traditional Simulator application within an Xcode installation. On newer Xcode setups where this new application is present, the lookup should prefer it over the older Simulator application.

## Expected Behavior

- When looking up the path to the simulator/device-hub application within Xcode, the tooling should first check whether the newer device management application exists in the Xcode bundle's Applications folder (at the same level as the Developer directory, not inside it).
- If the newer application is found, its path should be returned.
- If the newer application is not found, the tool should fall back to looking for the traditional Simulator application in its usual location.
- If neither application exists, the method should return null.
- If the Xcode installation path cannot be determined, the method should return null.

## Why This Matters

Flutter developers working with newer Xcode versions may have the new application available and should have Flutter tools correctly detect and use it. Without this change, the tooling always looks for the older application and may fail to find the right one on newer installations.
