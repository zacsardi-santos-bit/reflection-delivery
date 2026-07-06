Implement a Go client package for the SAP Alert Notification Service (ANS) to send structured events and verify service connectivity. Ensure the client is configurable via a service key and can handle event composition, validation, and severity mapping.

* Define the `ANS` struct in `pkg/ans/ans.go` with:
    * Fields: `XSUAA` of type `xsuaa.XSUAA`, and `URL` as a string.
    * Method `Send(event Event) error`:
        * Make an HTTP POST request to `<ANS.URL>/cf/producer/v1/resource-events`.
        * Include the bearer token in the `Authorization` header.
        * Set `Content-Type` to `application/json`.
        * Serialize the `Event` as the JSON body.
        * Return `nil` on HTTP 202; otherwise, return an error with "Did not get expected status code 202".
    * Method `CheckCorrectSetup() error`:
        * Make an HTTP GET request to `<ANS.URL>/cf/consumer/v1/matched-events`.
        * Include the bearer token in the `Authorization` header.
        * Set `Content-Type` to `application/json`.
        * Return `nil` on HTTP 200; otherwise, return an error with "Did not get expected status code 200".
    * Method `SetServiceKey(serviceKey ServiceKey)`:
        * Populate `XSUAA.OAuthURL`, `XSUAA.ClientID`, `XSUAA.ClientSecret`, and `ANS.URL` from `ServiceKey`.

* Define the `ServiceKey` struct in `pkg/ans/ans.go` with:
    * Fields: `Url`, `ClientId`, `ClientSecret`, `OauthUrl` with JSON tags `url`, `client_id`, `client_secret`, `oauth_url`.

* Implement `UnmarshallServiceKeyJSON(serviceKeyJSON string) (ServiceKey, error)` in `pkg/ans/ans.go`:
    * Parse a JSON string into a `ServiceKey`.
    * Return an error with "error unmarshalling ANS serviceKey" for invalid input.

* Implement `readResponseBody(response *http.Response) ([]byte, error)` in `pkg/ans/ans.go`:
    * Return body bytes from an HTTP response.
    * Return an error "did not retrieve an HTTP response" if response is nil.

* Define the `Event` struct in `pkg/ans/event.go` with:
    * Fields: `EventType`, `EventTimestamp`, `Severity`, `Category`, `Subject`, `Body`, `Priority`, `Tags`, `Resource`.
    * Method `MergeWithJSON(eventJSON []byte) error`:
        * Merge JSON fields into the `Event`.
        * Handle tag and resource field merging.
        * Return an error for unknown or malformed JSON fields.
    * Method `SetSeverityAndCategory(level logrus.Level)`:
        * Map log levels to ANS severity and category.
    * Method `Validate() error`:
        * Validate `Event` fields.
        * Return specific error messages for invalid fields.

* Define the `Resource` struct in `pkg/ans/event.go` with fields: `ResourceName`, `ResourceType`, `ResourceInstance`.

* Define package-level constants in `pkg/ans/event.go` or `pkg/ans/ans.go`:
    * `authHeaderKey = "Authorization"`
    * `infoSeverity = "INFO"`
    * `noticeSeverity = "NOTICE"`
    * `warningSeverity = "WARNING"`
    * `errorSeverity = "ERROR"`
    * `fatalSeverity = "FATAL"`
    * `exceptionCategory = "EXCEPTION"`
    * `alertCategory = "ALERT"`
    * `notificationCategory = "NOTIFICATION"`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.