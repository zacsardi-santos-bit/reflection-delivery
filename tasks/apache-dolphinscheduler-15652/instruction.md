Implement the necessary changes to improve clarity and security in the resources management service. Rename methods for better understanding, validate file paths for security, and remove obsolete methods.

*   Rename Methods:
    *   In `ResourcesService` interface, rename `createResource` to `uploadResource` with the signature: `(User loginUser, String name, ResourceType type, MultipartFile file, String currentDir) -> Result<Object>`.
    *   In `ResourcesService` interface, rename `onlineCreateResource` to `createResourceFile` with the signature: `(User loginUser, ResourceType type, String fileName, String fileSuffix, String content, String currentDirectory) -> Result<Object>`.
    *   In `ResourcesController`, rename the method handling `POST /resources/online-create` from `onlineCreateResource` to `createResourceFile`, and update it to call `resourceService.createResourceFile(...)`.
    *   Update `ResourcesController` to call `resourceService.uploadResource(...)` instead of `resourceService.createResource(...)` for resource uploads.

*   Implement Method Changes:
    *   In `ResourcesServiceImpl`, implement `uploadResource` replacing `createResource`. Ensure it returns specific status codes for various conditions:
        *   `Status.RESOURCE_FILE_IS_EMPTY` if the file is empty.
        *   `Status.RESOURCE_SUFFIX_FORBID_CHANGE` if the file extension changes.
        *   `Status.UDF_RESOURCE_SUFFIX_NOT_JAR` if a UDF resource lacks a `.jar` extension.
        *   `Status.RESOURCE_FULL_NAME_TOO_LONG_ERROR` if the full path exceeds the maximum length.
    *   Implement `createResourceFile` in `ResourcesServiceImpl` with the same behavior as the previous `onlineCreateResource`.

*   Validate Resource Paths:
    *   In `ResourcesServiceImpl.updateResourceContent`, validate that `fullName` starts with the storage directory for the tenant. If not, throw a `ServiceException` with the message: 'Internal Server Error: Resource file: <fullName> is illegal'.
    *   Ensure `updateResourceContent` returns:
        *   `Status.RESOURCE_NOT_EXIST` if the file status is null.
        *   `Status.RESOURCE_SUFFIX_NOT_SUPPORT_VIEW` if the file suffix is unsupported.
        *   `Status.USER_NOT_EXIST` if the user is not found.
        *   `Status.SUCCESS` if all validations pass and content is written.

*   Remove Obsolete Methods:
    *   Remove `authorizedUDFFunction` and `unauthorizedUDFFunction` from `ResourcesService` and `ResourcesServiceImpl`.
    *   Remove the corresponding endpoints `GET /resources/authed-udf-func` and `GET /resources/unauth-udf-func` from `ResourcesController`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.