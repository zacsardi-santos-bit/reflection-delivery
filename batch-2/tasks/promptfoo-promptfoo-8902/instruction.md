I'm working on improving how our telemetry system tracks user authentication state.

*   Must export a getUserAuthInfo() function from src/globalConfig/accounts.ts that returns a UserAuthInfo object with three fields: email (string or null), isLoggedIntoCloud (boolean), and authMethod ('api-key' | 'email' | 'none').

*   getUserAuthInfo() must read the global configuration exactly once per call. email must be taken from globalConfig.account.email, returning null if absent.

*   getUserAuthInfo() must set isLoggedIntoCloud to true when either globalConfig.cloud.apiKey is present or the PROMPTFOO_API_KEY environment variable is set, and false otherwise.

*   getUserAuthInfo() must set authMethod to 'api-key' when isLoggedIntoCloud is true, 'email' when only an email address is configured (no API key), and 'none' when neither is present.

*   Must export a UserAuthInfo interface and an AuthMethod type alias ('api-key' | 'email' | 'none') from src/globalConfig/accounts.ts.

*   When the Telemetry class sends a PostHog capture event, the properties object must include a $set key whose value is { email, isLoggedIntoCloud, authMethod, isRunningInCi }, reflecting the user's current authentication state and CI status.

*   The person properties included in $set must be fetched fresh on each event recording call by invoking getUserAuthInfo() once per event — not from a value cached at construction time.

*   The email field sent to the reporting endpoint (https://r.promptfoo.app/) must also come from the per-event getUserAuthInfo() result, not a constructor-cached value.


*   Interface details: Type: TypeAlias
Name: AuthMethod
Location: src/globalConfig/accounts.ts
Signature: type AuthMethod = 'api-key' | 'email' | 'none'
Description: String literal union representing the authentication method in use.

Type: Interface
Name: UserAuthInfo
Location: src/globalConfig/accounts.ts
Description: Object returned by getUserAuthInfo() describing the current user's authentication state.
Fields:
  email: string | null   — the user's email from global config, or null if absent
  isLoggedIntoCloud: boolean — true when an API key is present (from config or PROMPTFOO_API_KEY env var)
  authMethod: AuthMethod — 'api-key' | 'email' | 'none'

Type: Function
Name: getUserAuthInfo
Location: src/globalConfig/accounts.ts
Signature: getUserAuthInfo(): UserAuthInfo
Description: Reads the global configuration exactly once and returns a UserAuthInfo snapshot. email comes from globalConfig.account.email (null if missing). isLoggedIntoCloud is true if globalConfig.cloud.apiKey or the PROMPTFOO_API_KEY environment variable is set. authMethod is 'api-key' when isLoggedIntoCloud is true, 'email' when only an email is present, and 'none' otherwise. Must be exported.

Type: Class Method
Name: record (via sendEvent)
Location: src/telemetry.ts
Class: Telemetry
Description: When recording a telemetry event, the PostHog capture call must include a $set property containing the person properties { email, isLoggedIntoCloud, authMethod, isRunningInCi }. These values must be fetched fresh on each call to record() by calling getUserAuthInfo() once per event — not cached from the constructor. The email sent to the reporting endpoint (https://r.promptfoo.app/) must also come from the per-event getUserAuthInfo() call.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.