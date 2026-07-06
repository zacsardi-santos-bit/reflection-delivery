## Description

When Apache Pinot ingests data from a Kafka cluster that requires SSL/TLS authentication, operators must manually create and maintain trust store and key store files containing the appropriate certificates and private keys. There is currently no mechanism to supply raw certificate and key material directly through Kafka consumer configuration properties and have Pinot automatically construct and manage those store files.

## Expected Behavior

- Operators should be able to include Base64-encoded certificate and private key data directly in the stream consumer configuration properties.
- When the configuration is applied, the system should automatically create the trust store and key store files at the specified locations and populate them with the provided certificate/key material.
- Calling the initialization logic a second time with newly rotated certificate data should update the stores cleanly, without leaving duplicate entries.
- If the certificate properties are absent during a subsequent initialization call, any already-existing stores should be left completely untouched.
- When a trust store certificate is not provided but a key store is configured, the initialization should fail with a clear error indicating the trust store file could not be found.

## Why This Matters

Certificate rotation is a common operational requirement. Without this feature, every certificate renewal requires manual intervention to rebuild store files and restart or reconfigure Pinot's Kafka consumers. Automating store management from configuration properties enables certificate rotation with no manual file-system operations and improves operational reliability for SSL-secured Kafka pipelines.
