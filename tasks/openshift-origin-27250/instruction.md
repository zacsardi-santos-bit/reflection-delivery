Implement a mechanism to classify DNS lookup timeout errors as warnings rather than errors in backend availability sampling. Ensure that these events are clearly marked and include an automated check to detect and report DNS lookup timeout occurrences as a known flaky condition.

*   Update the backend disruption sampling logic:
    *   Classify events with error messages matching the DNS lookup i/o timeout pattern (e.g., 'dial tcp: lookup [hostname]: i/o timeout') as Warning level events instead of Error level.
    *   Ensure the event message for DNS lookup i/o timeout errors includes 'DNS lookup timeouts began' and retains the original error message details.
*   Define and use a constant for event classification:
    *   Create an exported string constant named `DisruptionSamplerOutageBeganEventReason` in the `pkg/monitor/backenddisruption` package.
    *   Embed this constant in DNS timeout event messages using a format that allows `monitorapi.ReasonFrom` to extract it (e.g., 'reason/<constant-value>' prefix).
*   Integrate a new automated check:
    *   Detect DNS lookup timeout events in disruption samplers during test runs.
    *   Report these events as a known flaky condition, not a hard failure.
    *   Include this check in the existing suite of upgrade event invariants.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.