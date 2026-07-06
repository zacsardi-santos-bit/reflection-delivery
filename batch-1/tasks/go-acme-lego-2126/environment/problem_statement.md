## Description

Lego currently has no support for the Shellrent hosting platform as a DNS provider for ACME DNS-01 challenges. Shellrent customers who want to automate certificate management through lego have no way to do so, because no integration with the Shellrent API exists.

This feature request is to add a new DNS provider for Shellrent so that users can supply their Shellrent credentials and have lego automatically create and remove the DNS records required during the ACME challenge process.

## Expected Behavior

- Users should be able to configure the provider using a username and API token, supplied either through environment variables or programmatic configuration.
- The provider should validate that both credentials are present and return clear error messages if either is missing.
- The provider should be able to list available services, retrieve details for a specific service and its associated domain, and create or delete DNS records on that domain.
- Because Shellrent's API only accepts a specific set of TTL values, any TTL provided to the provider must be automatically rounded up to the nearest supported value. Values that exceed the largest supported TTL should fall back to the smallest supported value.

## Why This Matters

Shellrent customers currently cannot automate SSL/TLS certificate renewal through lego. Adding this provider enables fully automated certificate lifecycle management for domains hosted on Shellrent.
