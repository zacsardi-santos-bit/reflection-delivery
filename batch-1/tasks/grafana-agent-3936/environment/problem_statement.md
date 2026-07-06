## Description

We need a new component that automatically synchronizes alerting and recording rule resources from Kubernetes into a Loki ruler instance. Currently, there is no automated way to discover rule definitions stored as Kubernetes custom resources and push them into Loki so the ruler can evaluate them. This has to be done manually or through separate tooling, which is error-prone and difficult to maintain.

## Expected Behavior

- The component should watch Kubernetes for rule resources across selectable namespaces and apply a labeling-based filter.
- When Kubernetes rule resources are added, updated, or removed, the component should detect those changes and apply the minimal set of operations to the Loki ruler API to bring it in sync with the desired state.
- The component should compute a diff between the desired state (derived from Kubernetes) and the current state (retrieved from Loki), and only send add, update, or remove operations for rule groups that have actually changed.
- The HTTP client used to talk to Loki must correctly handle rule namespace and group names that contain special characters such as spaces and forward slashes, encoding them properly in the URL so the API can correctly identify them.
- The component's configuration must support standard HTTP authentication options (basic auth, bearer token, OAuth2, etc.) while enforcing that at most one authentication method is used at a time.
- The component should use a configurable namespace prefix to distinguish rules it manages from those managed by other deployments.

## Why This Matters

Teams running Loki in a Kubernetes environment want to define alerting rules using the same Kubernetes-native custom resource definitions they already use for other monitoring systems, rather than managing Loki ruler configuration separately. This component closes that gap by acting as an automated bridge between Kubernetes rule resources and the Loki ruler API.
