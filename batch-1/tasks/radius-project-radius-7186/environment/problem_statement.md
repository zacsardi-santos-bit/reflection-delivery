## Description

When a developer runs the CLI command to deploy and run a Radius application, the tool currently only sets up automatic port-forwarding for the application itself. However, there is a Radius Dashboard — a web UI component that may be deployed alongside the Radius control plane in the cluster — that developers would also benefit from accessing locally without any manual setup.

## Expected Behavior

- When the CLI run command is invoked, if the Radius Dashboard is detected as running in the cluster, port-forwarding should be automatically established for both the application **and** the dashboard.
- If the dashboard is not found in the cluster, only the application port-forward should be started.
- The port-forwarding mechanism should identify target pods using Kubernetes label selectors rather than application name strings alone, making it consistent across both application and dashboard scenarios.
- There should be well-defined, reusable functions for constructing the label selectors used to identify application pods and dashboard pods respectively.

## Why This Matters

Developers running applications locally expect seamless access to both their app and any platform-level tooling. Having to manually set up port-forwarding for the dashboard is an extra friction point. Automatically detecting and forwarding the dashboard port means developers can open the UI as soon as they run their app, improving the development experience.
