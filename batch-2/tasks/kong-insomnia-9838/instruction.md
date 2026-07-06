I'm working on the curl import feature in Insomnia.

*   The `convert` function must be asynchronous, returning a Promise that resolves to the array of imported requests.

*   When `convert` processes a curl command that does not include any User-Agent header, it must append a header with name 'User-Agent' and value `insomnia/<version>` (where `<version>` is obtained from `getAppVersion()`) to each resulting request's headers array.

*   When a curl command already includes a User-Agent header (matched case-insensitively, e.g. 'User-Agent', 'user-agent'), `convert` must not inject the default User-Agent and must preserve the original header value unchanged.

*   When the application setting `disableAppVersionUserAgent` is `true`, `convert` must skip the default User-Agent injection entirely, resulting in no User-Agent header being added to requests that did not have one.

*   When `disableAppVersionUserAgent` is `true` but the curl command already includes an explicit User-Agent header, that explicit header must be preserved in the output.

*   The User-Agent value must be constructed as the string `insomnia/` concatenated with the return value of `getAppVersion()` from `packages/insomnia/src/common/constants.ts`, so that test mocks of `getAppVersion` are respected.


*   Interface details: Type: Function
Name: convert
Location: packages/insomnia/src/main/importers/importers/curl.ts
Signature: convert(rawData: string): Promise<ImportRequest[] | null>
Description: Converts a raw curl command string into an array of Insomnia ImportRequest objects. Must be async. Must inject a User-Agent header (name: 'User-Agent', value: `insomnia/<getAppVersion()>`) into each request that does not already have a User-Agent header (case-insensitive match). Must skip injection when the `disableAppVersionUserAgent` setting is true. Must preserve any explicitly provided User-Agent header from the curl command even when the setting is true.

Type: Function
Name: getAppVersion
Location: packages/insomnia/src/common/constants.ts
Signature: getAppVersion(): string
Description: Returns the current application version string. Used to compose the default User-Agent header value `insomnia/<getAppVersion()>`. This function must be imported from this module so that tests can mock it.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.