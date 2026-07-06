## Description

Gatus can monitor external services, but many real-world environments include services that are only accessible through a private network — for example, internal APIs or databases reachable only via SSH. Currently, there is no way to configure Gatus to route health checks through an SSH tunnel, making it impossible to monitor these hidden services.

This request adds SSH tunneling support to Gatus configuration, allowing users to define one or more named SSH tunnels and then associate any endpoint with a specific tunnel. At startup, the configuration loader should validate that every tunnel reference in every endpoint (including endpoints inside suites) actually refers to a defined tunnel — if not, startup should fail with a clear, specific error message that identifies exactly which endpoint has the invalid reference.

## Expected Behavior

- Users can define SSH tunnels by name in the configuration, specifying host, port, username, and authentication (either a password or a private key).
- Endpoint and suite-endpoint configurations can reference a tunnel by name, causing health-check traffic to be routed through that tunnel.
- If any endpoint references a tunnel name that does not exist in the tunneling configuration, the application must fail at startup with a descriptive error identifying the endpoint (or suite + endpoint) and the missing tunnel name.
- If no tunnel is configured globally, endpoints without tunnel references continue to work without any errors.
- SSH tunnel connections should be reused across health checks rather than recreated each time.

## Why This Matters

Many production environments have databases, internal APIs, and services behind firewalls that are only accessible over SSH. Without SSH tunnel support, Gatus cannot monitor these services at all, limiting its usefulness in private or hybrid-cloud environments.
