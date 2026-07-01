## Description

The stackset controller has been updated to a newer release that no longer supports the configuration option controlling how long to wait before removing an old ingress source when switching between routing mechanisms. Since this feature was removed from the controller, the corresponding entry in our cluster configuration defaults is now obsolete and should be cleaned up.

## Expected Behavior

- The cluster configuration defaults file should not contain any reference to the ingress source switch TTL setting, as the controller no longer recognizes or uses this parameter.
- The go module dependency on the stackset controller should be updated to the latest release, reflecting the version where this feature was removed.

## Why This Matters

Keeping a configuration key that no longer has any effect is misleading to operators and may cause confusion about what the cluster actually supports. Removing the dead configuration entry keeps the cluster configuration clean and aligned with what the current version of the stackset controller actually supports. This prevents operators from mistakenly believing they can tune this behavior, and avoids any potential errors if the controller rejects unknown configuration parameters in the future.
