Implement a compatibility layer within the Terraform Azure provider to manage Azure Data Factory pipelines with web activities containing expression-typed HTTP headers. Create a new Go package to wrap the existing SDK client, allowing flexible handling of header types.

*   Create a new Go package at `internal/services/datafactory/azuresdkhacks` with the package name `azuresdkhacks`.
    *   Ensure the package compiles successfully and can be imported by the datafactory service package.
*   Export a struct named `PipelinesClient` in `pipelines.go` with:
    *   A field `OriginalClient` of type `*datafactory.PipelinesClient` from the external kermit SDK.
    *   Implement a `Get` method with the signature: `Get(ctx context.Context, resourceGroupName string, factoryName string, pipelineName string, ifNoneMatch string) (PipelineResource, error)`.
        *   Delegate HTTP operations to `OriginalClient`.
    *   Implement a `CreateOrUpdate` method with the signature: `CreateOrUpdate(ctx context.Context, resourceGroupName string, factoryName string, pipelineName string, pipeline PipelineResource, ifMatch string) (PipelineResource, error)`.
*   Export a `PipelineResource` struct in `models.go`:
    *   Embed `autorest.Response` using the field name `Response` with JSON tag `"-"`.
    *   Embed `*Pipeline` using the field name `Pipeline` with JSON tag `"properties,omitempty"`.
*   Export a `Pipeline` struct in `models.go`:
    *   Support JSON unmarshaling of pipeline activities, including `WebActivity` activities with headers as either plain strings or structured JSON objects.
*   Export a `WebActivityTypeProperties` struct in `models.go`:
    *   Define the `Headers` field as `interface{}` to allow deserialization of both plain string header values and structured expression objects.
*   Export a `WebActivity` struct in `models.go`:
    *   Embed `*WebActivityTypeProperties` and implement the `datafactory.BasicActivity` interface.
    *   Implement custom `UnmarshalJSON` and `MarshalJSON` methods.
    *   Implement all interface methods required by `datafactory.BasicActivity`, such as `AsWebActivity`, `AsCopyActivity`, and `AsBasicActivity`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.