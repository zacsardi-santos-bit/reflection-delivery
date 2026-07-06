## Description

Several important AWS security best practices are not covered by any existing automated checks in the infrastructure scanner. Teams scanning their Terraform configurations have no way to detect the following misconfigurations:

- API Gateway stages (both v1 and v2) that are missing client certificate authentication
- API Gateway methods that have no request parameter validation configured
- CloudFront distributions that permit the outdated and insecure SSLv3 protocol for origin communication
- EMR clusters that lack a security configuration attachment
- OpenSearch and Elasticsearch domains where fine-grained access control is not fully enabled (either the top-level security option or the internal user database requirement is disabled)

## Expected Behavior

The scanner should flag each of these cases as a security policy violation. Specifically:
- An API Gateway stage without a client certificate configured should fail
- An API Gateway method without a request validator should fail
- A CloudFront distribution with an origin using SSLv3 should fail; distributions using only secure protocols or no custom origin SSL settings should pass
- An EMR cluster with no security configuration should fail
- An OpenSearch or Elasticsearch domain where either fine-grained access control is disabled or the internal user database is not enabled should fail

## Why This Matters

These misconfigurations represent real security risks — unauthenticated API traffic, unvalidated API inputs, use of deprecated SSL protocols, unencrypted EMR data, and overly permissive search domain access controls. Automating detection of these issues helps teams catch and remediate them before they reach production.
