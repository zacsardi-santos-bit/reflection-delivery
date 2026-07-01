## Description

Kusion's backend storage system supports several storage backends, but it currently lacks constructor functions for creating storage backend instances from their configuration objects. While backend configuration types and validation utilities exist, there is no standardized way to instantiate a ready-to-use backend from a configuration struct. This is particularly evident for the local filesystem backend, which has no constructor at all, but also affects the MySQL, Alibaba Cloud object storage, and Amazon cloud storage backends.

## Expected Behavior

- There should be a constructor for the local filesystem backend that takes a configuration object and returns a properly initialized storage instance with the configured path.
- There should be constructors for the MySQL, Alibaba Cloud OSS, and Amazon S3 backends that accept their respective configuration objects and return an initialized storage instance or an error if initialization fails.
- Each constructor should map the fields from the configuration object to the corresponding fields in the storage struct.

## Why This Matters

Without these constructors, the storage subsystem cannot be instantiated consistently from configuration, making it impossible to connect configuration parsing to actual storage backend usage. This is a blocking gap in the backend storage initialization pipeline.
