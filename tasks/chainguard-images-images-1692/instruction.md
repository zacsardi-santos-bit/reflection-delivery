Create a new container image module for the Caddy web server to resolve the structural validation error and unblock the Go-based lint tests. Implement the necessary build configuration, Terraform modules, and image registration to ensure the image is fully functional and compliant with the specified requirements.

*   Create a new directory at `images/caddy/` for the Caddy web server container image.
    *   Include all required Terraform and configuration files to ensure the repository's structural lint validation passes.
*   Update the root `main.tf` file:
    *   Add a module declaration for 'caddy' that references the `./images/caddy` directory.
    *   Ensure the module uses a `target_repository` variable.
*   Populate the `images/caddy/` directory:
    *   Create a `main.tf` file defining the Caddy image module using the publisher pattern.
    *   Add a `config` subdirectory containing its own `main.tf` and a template `apko` YAML configuration file.
*   Configure the `apko` image:
    *   Include the Caddy package.
    *   Set up a non-root user account and configure the container to run as that user.
    *   Define `/usr/bin/caddy` as the entrypoint command and set 'run' as the default container command.
*   Ensure the Caddy image supports:
    *   A 'version' subcommand invocation that prints version information when run with 'version' as an argument.
    *   Accepting 'run --config /etc/caddy/Caddyfile' as arguments to load a specified configuration file and serve files from the configured path (e.g., `/usr/share/caddy`).
*   Verify that when the Caddy container is started with a Caddyfile configuring a file server on port 80, the server responds to HTTP requests with the content of the served files.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.