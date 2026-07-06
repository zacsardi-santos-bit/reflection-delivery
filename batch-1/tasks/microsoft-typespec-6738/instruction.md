Implement the handling of implicit or single-page paging operations in the TypeSpec C# client code generator. Ensure the generator correctly processes operations marked as pageable without a next-link URL or continuation token, producing valid collection result wrapper classes.

*   Modify the `CollectionResultDefinition` class located at `packages/http-client-csharp/generator/Microsoft.TypeSpec.Generator.ClientModel/src/Providers/CollectionResultDefinition.cs`:
    *   Ensure it generates four collection result types: `{OperationName}CollectionResult`, `{OperationName}AsyncCollectionResult`, `{OperationName}CollectionResultOfT`, and `{OperationName}AsyncCollectionResultOfT` for operations with no next-link and no continuation token.
    *   Implement `{OperationName}CollectionResult` as an internal partial class extending `CollectionResult`:
        *   Include private readonly fields for the client, required operation parameters, and request options.
        *   Validate required parameters in the constructor using `AssertNotNull`.
        *   Override `GetRawPages()` to create and process a single request message synchronously, yielding one `ClientResult`.
        *   Override `GetContinuationToken()` to return null.
    *   Implement `{OperationName}AsyncCollectionResult` as an internal partial class extending `AsyncCollectionResult`:
        *   Include the same fields and constructor as the synchronous class.
        *   Override `GetRawPagesAsync()` to process the request message asynchronously using `ProcessMessageAsync` with `ConfigureAwait(false)`, yielding one `ClientResult`.
        *   Override `GetContinuationToken()` to return null.
    *   Implement `{OperationName}CollectionResultOfT` as an internal partial class extending `CollectionResult<T>`:
        *   Override `GetRawPages()` and `GetContinuationToken()` (returning null).
        *   Override `GetValuesFromPage()` to cast the page to the response model type and return its items property.
    *   Implement `{OperationName}AsyncCollectionResultOfT` as an internal partial class extending `AsyncCollectionResult<T>`:
        *   Override `GetRawPagesAsync()` and `GetContinuationToken()` (returning null).
        *   Override `GetValuesFromPageAsync()` as an async method to cast the page, iterate its items, yield each item, and call `Task.Yield()` between yields.
    *   Use `BuildGetRawPagesForSingle()` for the raw pages method body and `BuildGetContinuationToken()` to return null when both next-link and continuation token are null.
    *   Replace null-forgiving assertions on `ContinuationToken` with null-conditional access.

*   Update the `ScmMethodProviderCollection` class located at `packages/http-client-csharp/generator/Microsoft.TypeSpec.Generator.ClientModel/src/Providers/ScmMethodProviderCollection.cs`:
    *   Change the `_isPageable` field assignment to treat any operation with non-null paging metadata (`operation.Paging != null`) as pageable, regardless of next-link or continuation token presence.
    *   Ensure operations with paging metadata but no next-link and no continuation token produce exactly four methods in the method collection.
    *   Ensure the convenience method without an 'options' parameter returns a `CollectionResult`.

*   Ensure operations with no paging metadata are not treated as pageable and do not produce `CollectionResultDefinition` type providers.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.