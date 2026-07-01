## Description

The proxy currently sends the same set of security-related HTTP response headers on every connection, including a header that instructs browsers to enforce HTTPS-only access for a long period. Sending this HTTPS-enforcement header on plain (non-TLS) connections is incorrect and can cause browsers to refuse to connect to the site over plain HTTP even when that is intended. The header should only be sent when the connection is actually secured with a TLS certificate.

Additionally, there is no clean way for operators to fully disable the default set of security headers when they want to manage those headers themselves (e.g., at a load balancer layer in front of pomerium).

## Expected Behavior

- When a request arrives over a TLS-secured connection (i.e., the server has a certificate for the domain), the full set of default security headers — including the long-term HTTPS enforcement header — should be included in the response.
- When a request arrives over a plain HTTP connection, the default security headers should be included but the HTTPS enforcement header should be omitted.
- Operators should be able to disable all default security response headers entirely by setting a special disable key in the response headers configuration.
- The default options should no longer hard-code the security headers as static configuration; instead, headers should be determined dynamically based on connection context.

## Why This Matters

Sending HTTPS enforcement headers on non-TLS connections is a misconfiguration that can lock users out of plain-HTTP access. Giving operators a way to completely opt out of the default security headers is also important for deployments where another component handles these headers.
