## Description

There are several related improvements needed in the system service lifecycle management, maintenance mode configuration handling, and configuration validation.

**Service lifecycle visibility**: Services currently do not report a distinct "starting" state when they begin execution. This makes it harder to observe and reason about what a service is doing at any given moment, particularly when a service is restarted repeatedly. There is no clean way to wait for a service to begin running before interacting with it.

**Maintenance mode config persistence**: When a partial configuration is applied via the maintenance API, the configuration is not being persisted in the state store under a dedicated resource. Additionally, when the maintenance service shuts down, it leaves behind any configuration resources it may have created — these should be cleaned up automatically.

**Config container patching**: There is no clean way to patch only the primary portion of a multi-document configuration container without manually extracting, modifying, and reconstructing all the other documents. This leads to repeated boilerplate and potential for accidentally dropping secondary config documents (such as SideroLink configuration).

**Validation gap for worker nodes**: Configuration validation currently allows etcd-specific settings to be specified on worker-type machines without emitting any error. This is incorrect — etcd configuration is only meaningful on control plane nodes and should be rejected outright when specified for a worker.

## Expected Behavior

- Services must enter a clearly observable "starting" state at the beginning of each run, before any other lifecycle transitions.
- It must be possible to wait synchronously for a service to enter the "starting" state before returning to the caller, avoiding race conditions when starting and immediately stopping services.
- When a partial config is applied in maintenance mode, a config resource should be created and held in state, accessible by other components, for the duration of maintenance.
- When maintenance mode ends, the temporary config resource must be removed automatically.
- A method should exist to patch the primary config section of a multi-document config container while automatically preserving all other documents.
- If etcd configuration is specified for a worker-type machine, validation must return an error making clear that such settings are only permitted on control plane machines.

## Why This Matters

These changes improve operational clarity (better service state visibility), correctness (configuration is cleaned up after maintenance, validation catches misconfiguration earlier), and developer ergonomics (less boilerplate when patching config containers).
