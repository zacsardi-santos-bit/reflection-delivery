I'm working with a Helm deployment helper in our CI/CD pipeline and running into a few issues with how it handles chart sources and repository operations.

First, the upgrade and install operations currently fail if no local chart path is configured — but we often deploy directly from a remote chart repository without a local checkout. I'd like the behavior to be: if a local chart path is provided, use it and skip the repository registration step; if no chart path is provided, automatically register the configured remote repository and use it as the chart source instead.

Second, the uninstall operation unnecessarily tries to register a chart repository before uninstalling, which fails when repository credentials aren't set up. Uninstall doesn't need to touch the chart repository at all — it should just run the uninstall directly. It should also validate upfront that a namespace has been provided, returning a clear error message indicating that the namespace parameter is missing if it hasn't.

Could you update the upgrade, install, and uninstall logic to handle these cases correctly?
