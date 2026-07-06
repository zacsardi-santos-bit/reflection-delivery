## Description

Medplum's AWS deployment tooling currently supports a third-party antivirus scanner for S3 storage, but AWS now provides a native malware protection service that can gate file access based on scan results. We need to add support for this native scanning service so that deployments can enable it as a replacement for the older antivirus approach.

## Expected Behavior

- The deployment configuration should accept an option to enable the native AWS malware protection service for the storage bucket.
- When this protection is enabled, the storage bucket's access policy should automatically include a deny rule that blocks CloudFront from serving any objects that have not been scanned and confirmed safe (i.e., objects lacking a scan-clean status tag).
- The deny rule must not be duplicated if it already exists and is complete. However, if an existing deny rule is incomplete or malformed, a new correct one should be added.
- The bucket policy update command should be resilient to partial failures: if one bucket's policy update fails (e.g., because a conflicting policy already exists), the command should log the error and continue updating the remaining buckets, completing with a "Done" message rather than halting entirely.

## Why This Matters

Deployments that rely on the older scanning approach cannot easily migrate to the newer AWS-native service without manually configuring S3 bucket policies. This change allows operators to simply enable the feature in the deployment config and have the correct access-control policies applied automatically. The added resilience in error handling also prevents a single stale or misconfigured bucket from blocking the entire update operation.
