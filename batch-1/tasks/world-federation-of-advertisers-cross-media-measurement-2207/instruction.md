Implement support for "wrapping" sampling intervals in the cross-media measurement system to accommodate the shuffle-based distributed protocol. Update validation logic, utility functions, and simulators to handle these intervals correctly.

*   Update the `computeUnionSamplingWidth` function:
    *   Make it public in `Covariances.kt`.
    *   Accept two `VidSamplingInterval` arguments and return their union width as a `Double`.
    *   Handle all overlap scenarios, including wrapping intervals, with accuracy within 1e-9.

*   Modify the `withDefaults` function in `MetricSpecDefaults.kt`:
    *   Add a `Boolean` parameter `allowSamplingIntervalWrapping` with a default of `false`.
    *   Allow wrapping intervals if `allowSamplingIntervalWrapping` is `true` and width <= 1.0.
    *   Throw `MetricSpecDefaultsException` with message containing "vidSamplingInterval" and cause `IllegalArgumentException` if conditions are not met.

*   Adjust `sampleVids` function in `SampleVids.kt`:
    *   Change validation to allow `vidSamplingIntervalWidth <= 1`, permitting wrapping intervals.

*   Update `MeasurementConsumerSimulator` class:
    *   Add `vidSamplingInterval: VidSamplingInterval = DEFAULT_VID_SAMPLING_INTERVAL` to public methods:
        *   `testReachAndFrequency`
        *   `testInvalidReachAndFrequency`
        *   `executeReachOnly`
        *   `executeReachAndFrequency`
        *   `testReachOnly`
    *   Define `DEFAULT_VID_SAMPLING_INTERVAL` with `start = 0.2f` and `width = 0.5f`.
    *   Modify internal spec builder functions to accept `vidSamplingInterval`.
    *   Update `createMeasurementInfo` to accept `vidSamplingInterval` and adjust `newMeasurementSpec` signature.

*   Ensure the Measurements service:
    *   Accepts wrapping intervals with HMSS protocol and creates measurements successfully.
    *   Rejects wrapping intervals with non-HMSS protocols, returning gRPC status `INVALID_ARGUMENT` with "VidSamplingInterval" in the message.

*   Conduct an end-to-end integration test:
    *   Verify successful creation and result of a reach-and-frequency measurement using HMSS protocol with a wrapping interval (start=0.5f, width=1.0f).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.