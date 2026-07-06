## Description

When Pulumi runs a deployment, the engine performs an up-front installation check for all plugins that the language host reports as required by the program. However, the language host is over-inclusive — it lists every package the program imports, not just those whose resources are actually registered during that particular run. This means the engine checks and potentially installs packages that are imported as a library but never used to create any resources, adding unnecessary overhead and potential failure points.

## Expected Behavior

There should be a way to skip the up-front plugin pre-installation step. When this option is enabled:

- The engine should not perform any up-front check or installation for plugins reported by the language host
- Plugins that are truly needed at runtime should still be loaded on demand when the program actually registers a resource of that type
- Unused plugins (imported but never creating resources) should not be loaded at all

When the option is disabled (the default), the existing behavior should be preserved: all packages reported by the language host are checked for installation before the deployment proceeds.

## Why This Matters

Programs often import provider packages for type information or utility functions without ever creating resources from those packages. The current behavior forces the engine to try to install every imported package, even those that will never create a resource in this run. A lazy-loading approach avoids this unnecessary work and makes deployments faster and more robust for programs that import many packages.
