## Description

The lego ACME client currently lacks support for Webnames.ru as a DNS provider, which prevents users with domains registered there from using lego to automate SSL certificate issuance and renewal via DNS-01 challenges.

## Expected Behavior

- Users should be able to supply a Webnames API key via an environment variable to configure the provider.
- The provider should automatically add DNS TXT records to prove domain ownership during certificate requests.
- The provider should automatically remove those DNS TXT records once the challenge is complete.
- If the API key environment variable is not set, initialization should fail with a clear error message indicating which credential is missing.
- If the API key is not supplied when configuring the provider programmatically, initialization should fail with a clear error indicating that credentials are missing.

## Why This Matters

Webnames.ru is a domain registrar used by many Russian-speaking users. Without this provider, those users must manually manage DNS records during certificate issuance, which defeats the purpose of automated certificate renewal. Adding this provider brings Webnames users the same automation capabilities available to users of other supported registrars.
