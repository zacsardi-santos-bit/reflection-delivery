Implement robustness and API consistency improvements in the Braintree Android SDK's 3D Secure module. Ensure null inputs produce descriptive exceptions or failure results, update method signatures for HTTP POST and analytics calls, and enforce explicit field values in data classes.

*   Update the `continueLookup` method in `CardinalClient`:
    *   Throw a `BraintreeException` with the message "challengeObserver is null" if the `challengeObserver` parameter is null.

*   Modify the `tokenize` method in `ThreeDSecureClient`:
    *   Detect if `threeDSecureParams` or `jwt` in `ThreeDSecurePaymentAuthResult` is null.
    *   Return a `ThreeDSecureResult.Failure` with the error message "threeDSecureParams or jwt is null".
    *   Send analytics events `JWT_AUTH_FAILED` and `VERIFY_FAILED` using a new `AnalyticsEventParams()` instance.

*   Update the `sendAnalyticsEvent` method in `BraintreeClient`:
    *   Accept a second parameter of type `AnalyticsEventParams`.
    *   Ensure all calls within the ThreeDSecure module pass a new `AnalyticsEventParams()` instance as this second argument.

*   Revise the `sendPOST` method in `BraintreeClient`:
    *   Include a `Map<String, String>?` parameter for headers between the data body and the callback.
    *   Ensure all ThreeDSecure module POST calls use the updated 4-argument form: `sendPOST(url, data, headersMap, callback)`.

*   Adjust the `ThreeDSecureLauncher` component:
    *   Provide a `setActivityLauncher` method accepting an `ActivityResultLauncher<ThreeDSecureParams?>`.
    *   Replace any previous direct field assignments to `activityLauncher`.

*   Enforce explicit field values in `ThreeDSecureParams`:
    *   Require all three constructor parameters: `threeDSecureNonce`, `errorMessage`, and `lookup`.
    *   Remove any default values, ensuring callers provide explicit arguments or null for each field.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.