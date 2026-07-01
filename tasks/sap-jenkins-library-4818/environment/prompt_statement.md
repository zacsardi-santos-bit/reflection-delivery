I'm working on adding a new pipeline step to our CI/CD library that integrates with Contrast Security for runtime application security testing. We want to be able to pull vulnerability findings from the Contrast Security platform and use them as part of our automated security gate logic.

The step needs to handle configuration validation — all the required credentials and application identifiers should be checked upfront, and the server URL should be automatically normalized to include the secure protocol prefix if it's missing. The step should also generate the correct API and GUI URLs from the configured server, organization, and application identifiers.

For authentication, we need to produce an encoded credential string from the username and service key. Vulnerability data should be fetched from the API with full support for paginated responses, so that all pages are retrieved automatically.

The findings should be grouped into two classification tiers based on severity: a higher-priority group for critical, high, and medium severity issues, and a lower-priority group for low and informational issues. Within each group, we need to track both the total count and the count that have been reviewed — a finding is considered reviewed if its status is anything other than newly reported.

Finally, the step should produce a structured tool record for the scanned application. The record should capture the application name, URL, server, and identifier, with the application identifier being required — an error should be returned if it's missing. The tool record should also include the server as the tool instance and a single key entry describing the application.
