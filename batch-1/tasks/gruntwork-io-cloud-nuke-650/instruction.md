Implement support for discovering and deleting S3 access points in cloud-nuke, including standard, object lambda, and multi-region variants. Ensure that each type respects appropriate filtering rules and integrates with the existing configuration system.

*   Implement the `S3AccessPoint` struct in `aws/resources/s3_access_point_types.go`:
    *   Define methods:
        *   `getAll(c context.Context, configObj config.Config) ([]*string, error)`: Retrieve S3 access points using the S3 control API, apply name-based regex exclusion filters, and return matching access point names.
        *   `nukeAll(identifiers []*string) error`: Delete specified access points using the S3 control API.

*   Implement the `S3ObjectLambdaAccessPoint` struct in `aws/resources/s3_object_lambda_access_point_types.go`:
    *   Define methods:
        *   `getAll(c context.Context, configObj config.Config) ([]*string, error)`: Retrieve object lambda access points using the S3 control API, apply name-based regex exclusion filters, and return matching access point names.
        *   `nukeAll(identifiers []*string) error`: Delete specified access points using the S3 control API.

*   Implement the `S3MultiRegionAccessPoint` struct in `aws/resources/s3_multi_region_access_point_types.go`:
    *   Define methods:
        *   `getAll(c context.Context, configObj config.Config) ([]*string, error)`: Retrieve multi-region access points using the S3 control API, apply both name-based regex and time-based exclusion filters, and return matching access point names.
        *   `nukeAll(identifiers []*string) error`: Delete specified access points using the S3 control API.

*   Update the `Config` struct in `config/config.go`:
    *   Add fields for new resource types:
        *   `S3AccessPoint` of type `ResourceType` with YAML tag "S3AccessPoint".
        *   `S3ObjectLambdaAccessPoint` of type `ResourceType` with YAML tag "S3ObjectLambdaAccessPoint".
        *   `S3MultiRegionAccessPoint` of type `ResourceType` with YAML tag "S3MultiRegionAccessPoint".

*   Ensure all `getAll` methods:
    *   Use `util.AccountIdKey` to read the AWS account ID from the context.
    *   Store the account ID in the struct's `AccountID` field for deletion operations.
    *   For `S3MultiRegionAccessPoint`, exclude access points created after the `TimeAfter` filter specified in the config.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.