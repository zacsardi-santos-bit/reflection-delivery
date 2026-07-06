Implement a new integration module for the Chronicle Backstory security platform that uses a streaming API to receive alerts in real time. Validate configuration parameters, handle connectivity and errors, and manage streaming with automatic reconnection and exponential back-off.

*   Implement the module in `Packs/GoogleChronicleBackstory/Integrations/GoogleChronicleBackstoryStreamingAPI/GoogleChronicleBackstoryStreamingAPI.py` and export the following: `Client`, `validate_configuration_parameters`, `parse_error_message`, `validate_response`, `test_module`, `fetch_samples`, `stream_detection_alerts_in_retry_loop`, `DATE_FORMAT`, `MAX_CONSECUTIVE_FAILURES`, `MAX_DELTA_TIME_FOR_STREAMING_DETECTIONS`, `MESSAGES`, `service_account`, `auth_requests`, `timezone`, `timedelta`.

*   Client Class:
    *   Instantiate with `params` (dict with 'credentials' and optional 'region'), `proxy` (bool), and `disable_ssl` (bool).
    *   Expose `region` attribute with the resolved region prefix string.

*   Configuration Validation:
    *   `validate_configuration_parameters(params: dict, command: str)`:
        *   Ensure `credentials` is valid JSON and `first_fetch` is within 7 days.
        *   Raise `ValueError` for invalid JSON or out-of-range `first_fetch`.

*   Error Handling:
    *   `parse_error_message(error: str, region: str)`:
        *   Return appropriate messages from `MESSAGES` based on error content and region.

*   Response Validation:
    *   `validate_response(client: Client, url: str, method: str = 'GET', body=None)`:
        *   Return empty dict for 200 OK with empty JSON.
        *   Raise `ValueError` for specific HTTP errors and non-JSON responses.

*   Connectivity Test:
    *   `test_module(client_obj: Client, params: dict)`:
        *   Return 'ok' on successful connection; return error string on failure.

*   Sample Retrieval:
    *   `fetch_samples()`:
        *   Return list from 'sample_events' in integration context or an empty list if absent.

*   Streaming Management:
    *   `stream_detection_alerts_in_retry_loop(client: Client, initial_continuation_time: datetime, test_mode: bool = False)`:
        *   Handle streaming with retries and back-off.
        *   Raise `RuntimeError` after exceeding `MAX_CONSECUTIVE_FAILURES`.

*   Constants:
    *   `DATE_FORMAT`: '%Y-%m-%dT%H:%M:%S.%fZ'
    *   `MAX_CONSECUTIVE_FAILURES`: 7
    *   `MAX_DELTA_TIME_FOR_STREAMING_DETECTIONS`: '7 days'
    *   `MESSAGES`: Include keys for 'INVALID_JSON_RESPONSE', 'INVALID_REGION', 'CONSECUTIVELY_FAILED', 'INVALID_ARGUMENTS'.

*   Module-level Imports:
    *   Import `service_account` from `google.oauth2.service_account`.
    *   Import `auth_requests` from `google.auth.transport.requests`.
    *   Import `timezone` and `timedelta` from `datetime`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.