Implement a new module for the testcontainers-go library that provides functionality for running a socat relay container. This module should allow users to configure forwarding targets, manage port mappings, and retrieve accessible URLs for testing purposes.

Requirements:

* Define a `Target` struct in `modules/socat/options.go`:
    * Include unexported fields: `exposedPort int`, `internalPort int`, and `host string`.
    * Implement a method `ExposedPort() int` that returns the `exposedPort` field value.

* Implement the `NewTarget` function in `modules/socat/options.go`:
    * Signature: `NewTarget(exposedPort int, host string) Target`.
    * Set both `exposedPort` and `internalPort` fields to `exposedPort`, and `host` to the provided host string.

* Implement the `NewTargetWithInternalPort` function in `modules/socat/options.go`:
    * Signature: `NewTargetWithInternalPort(exposedPort int, internalPort int, host string) Target`.
    * Set `exposedPort` and `host` fields to the provided values.
    * Set `internalPort` to `internalPort`, or to `exposedPort` if `internalPort` is 0.

* Define an unexported `options` struct in `modules/socat/options.go`:
    * Ensure it is instantiatable as `options{}` within the package.

* Define an `Option` type in `modules/socat/options.go`:
    * Type: `func(*options) error`.
    * Implement `testcontainers.ContainerCustomizer` by defining a `Customize(*testcontainers.GenericContainerRequest) error` method.

* Implement the `WithTarget` function in `modules/socat/options.go`:
    * Signature: `WithTarget(target Target) Option`.
    * Return an `Option` that adds the target to the configuration.
    * Return an error if `target.exposedPort` is 0.

* Define a `Container` struct in `modules/socat/socat.go`:
    * Embed `testcontainers.Container`.
    * Store a map of target URLs.
    * Implement `TargetURL(exposedPort int) *url.URL` to return the URL for the given port or nil if not mapped.

* Implement the `Run` function in `modules/socat/socat.go`:
    * Signature: `Run(ctx context.Context, img string, opts ...testcontainers.ContainerCustomizer) (*Container, error)`.
    * Start a socat relay container using the provided image and options.
    * Return the running `*Container` or an error.

* Ensure the module is located at `modules/socat` with package name `socat` and module path `github.com/testcontainers/testcontainers-go/modules/socat`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.