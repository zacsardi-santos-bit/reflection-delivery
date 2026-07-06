## Description

We need to add support for API Gateway client certificates in the cloud governance framework. Currently, there is no way to:

1. Discover and manage API Gateway client certificates as a first-class resource type
2. Filter API Gateway stages based on properties of their associated client certificate (such as whether the certificate has expired)

This gap makes it impossible to write governance policies that identify stages using expired client certificates, or to report on all client certificates in use.

## Expected Behavior

- Users should be able to write a policy that lists all API Gateway client certificates as a standalone resource type, with full access to certificate fields including expiration information.
- Users should be able to filter API Gateway stages by properties of their associated client certificate — for example, finding all stages whose certificate has already expired.
- When a stage matches based on its client certificate, the matched certificate data should be attached to the stage resource so it can be used in subsequent actions or reporting.
- The client certificate filter on stages should work whether the stage resources are sourced directly from the AWS API or from AWS Config.

## Why This Matters

API Gateway client certificates are used for mutual TLS authentication with backend integrations. When certificates expire, those integrations may fail. Organizations need a way to proactively identify stages with expiring or expired certificates and take action before they cause outages.
