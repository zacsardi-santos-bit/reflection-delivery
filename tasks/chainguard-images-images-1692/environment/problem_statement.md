## Description

We need to add a new container image for the Caddy web server to our catalog of hardened, minimal container images. The test infrastructure files for this image have already been added to the repository, but the image implementation itself (build configuration, Terraform modules, and image registration) is still missing. Because the test directory exists without a corresponding complete image setup, the repository's structural validation is now failing and blocking other unrelated tests from running.

## Expected Behavior

- A fully configured Caddy image module should exist in its dedicated directory, including the necessary Terraform build modules and a standard declarative image configuration.
- The image should run as a non-root user for security, with the Caddy binary as the container entrypoint and "run" as the default command.
- The image must be registered in the root build configuration so it is built and published automatically.
- The image should support a version query (running the image with a version argument should succeed).
- The image should be able to start a Caddy server using a custom configuration file provided at runtime, and serve static files from the configured web root.

## Why This Matters

Until this is implemented, the repository's lint/validation check fails with an error indicating the caddy module is not properly registered, which prevents the Go-based linting test suite from running at all. Adding the full image implementation will unblock those tests and provide a usable, hardened Caddy image for users who need a minimal web server container.
