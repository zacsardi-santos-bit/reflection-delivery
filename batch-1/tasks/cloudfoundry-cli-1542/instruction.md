Implement dynamic filtering in the Cloud Foundry CLI to ensure compatibility with various versions of the Cloud Controller API. Modify the functions that retrieve organizations and spaces by their unique identifiers to decide at runtime whether to use server-side or client-side filtering based on the API version.

*   Update `GetOrganizationsByGUIDs` in `actor/v3action/organization.go`:
    *   Check the Cloud Controller API version at runtime.
    *   Use server-side filtering with a GUID filter query if the version is 3.56.0 or higher.
    *   Omit query parameters and perform client-side filtering if the version is below 3.56.0.
    *   Return an empty organization slice on error, along with any warnings.

*   Update `GetSpacesByGUIDs` in `actor/v3action/space.go`:
    *   Check the Cloud Controller API version at runtime.
    *   Use server-side filtering with a GUID filter query if the version is 3.56.0 or higher.
    *   Omit query parameters and perform client-side filtering if the version is below 3.56.0.
    *   Default to client-side filtering if the version string is invalid or unparseable, without returning an error.

*   Add a constant `MinVersionSpacesGUIDsParamV3` in `api/cloudcontroller/ccversion/minimum_version.go`:
    *   Set the value to "3.56.0".
    *   Use this constant to determine the minimum API version that supports GUID filtering for both organizations and spaces.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.