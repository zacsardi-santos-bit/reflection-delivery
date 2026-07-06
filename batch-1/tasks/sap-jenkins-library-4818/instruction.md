Implement a new pipeline step to integrate Contrast Security scan results into your CI/CD pipeline. Ensure the step can authenticate, fetch, and classify vulnerabilities, and produce a structured tool record for applications.

*   Register the `contrastExecuteScan` step in the pipeline library with the identifier 'contrastExecuteScan'.

*   Implement the `getAuth` function:
    *   Return a base64-encoded string of "{Username}:{ServiceKey}" from the configuration.

*   Implement the `getApplicationUrls` function:
    *   Return two URL strings: 
        *   API URL: "{Server}/api/v4/organizations/{OrganizationID}/applications/{ApplicationID}"
        *   GUI URL: "{Server}/Contrast/static/ng/index.html#/{OrganizationID}/applications/{ApplicationID}"

*   Implement the `validateConfigs` function:
    *   Return a non-nil error if any of these fields are empty: UserAPIKey, Username, ServiceKey, Server, OrganizationID, ApplicationID.
    *   Prepend 'https://' to the Server field if it does not start with 'https://'.

*   Implement the `getApplicationFromClient` function:
    *   Call `ExecuteRequest` with a `*ApplicationResponse` as the destination parameter.
    *   Return `*ApplicationInfo` with Id and Name populated; Url and Server default to empty strings.

*   Implement the `getVulnerabilitiesFromClient` function:
    *   Support paginated responses by fetching all pages until the response's Last field is true.
    *   Return an empty `[]ContrastFindings` if the response's Empty field is true.
    *   Return an error if the request fails.

*   Implement the `getFindings` function:
    *   Classify vulnerabilities into two `ContrastFindings`:
        *   `AuditAll` for CRITICAL, HIGH, and MEDIUM severities.
        *   `Optional` for LOW and NOTE severities.
    *   Count a vulnerability as Audited if its Status is any value except 'REPORTED'.

*   Implement the `accumulateFindings` function:
    *   Iterate over the findings slice, adding `auditAllFindings.Total/Audited` to entries with `ClassificationName` equal to `AuditAll`.
    *   Add `optionalFindings.Total/Audited` to entries with `ClassificationName` equal to `Optional`.

*   Define constants:
    *   `AuditAll`: "Audit All"
    *   `Optional`: "Optional"

*   Implement the `createToolRecordContrast` function:
    *   Return a tool record with:
        *   `ToolName`: 'contrast'
        *   `ToolInstance`: appInfo.Server
        *   `DisplayName`: appInfo.Name
        *   `DisplayURL`: appInfo.Url
    *   Add one key entry via `AddKeyData` with name 'application', value=appInfo.Id, displayName=appInfo.Name, url=appInfo.Url.
    *   Return an error if appInfo.Id is empty.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.