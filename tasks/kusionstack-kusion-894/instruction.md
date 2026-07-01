Implement constructor functions for Kusion's backend storage system to create storage backend instances from configuration objects. Ensure each backend type—local filesystem, MySQL, Alibaba Cloud OSS, and Amazon S3—has a constructor that initializes the storage instance or returns an error if initialization fails.

*   Implement `NewLocalStorage` in `pkg/backend/storages/local.go`:
    *   Accept a `*v1.BackendLocalConfig` parameter.
    *   Return a `*LocalStorage` with the `path` field set to `config.Path`.
    *   Ensure `LocalStorage` is defined with an unexported string field `path`.

*   Implement `NewMysqlStorage` in `pkg/backend/storages/mysql.go`:
    *   Accept a `*v1.BackendMysqlConfig` parameter.
    *   Return a `(*MysqlStorage, error)`.
    *   Open a GORM database connection using `DBName`, `User`, `Host`, and `Port` from the config.
    *   Return a non-nil error if the connection fails.

*   Implement `NewOssStorage` in `pkg/backend/storages/oss.go`:
    *   Accept a `*v1.BackendOssConfig` parameter.
    *   Return a `(*OssStorage, error)`.
    *   Initialize an Alibaba Cloud OSS client using `Endpoint`, `AccessKeyID`, `AccessKeySecret`, and `Bucket` from the config.
    *   Return a non-nil error if client creation fails.

*   Implement `NewS3Storage` in `pkg/backend/storages/s3.go`:
    *   Accept a `*v1.BackendS3Config` parameter.
    *   Return a `(*S3Storage, error)`.
    *   Initialize an AWS session using `AccessKeyID`, `AccessKeySecret`, `Bucket`, and `Region` from the config.
    *   Return a non-nil error if session creation fails.

*   Ensure existing validation tests for OSS and S3 configurations accept 'kusion' as a valid bucket name.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.