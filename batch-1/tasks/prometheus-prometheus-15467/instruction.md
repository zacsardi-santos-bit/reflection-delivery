Implement support for custom bucket boundaries in Prometheus histograms within the write-ahead log (WAL). Ensure that these custom boundaries are preserved during encoding and decoding processes, and that all relevant components of the system handle these new record types correctly.

*   Define new WAL record type constants:
    *   `CustomBucketsHistogramSamples` with value 9 for integer histograms.
    *   `CustomBucketsFloatHistogramSamples` with value 10 for float histograms.

*   Modify encoding methods:
    *   Update `Encoder.HistogramSamples` to return a buffer of standard histograms and a slice of custom-bucket histograms.
    *   Update `Encoder.FloatHistogramSamples` similarly for float histograms.
    *   Implement `Encoder.CustomBucketsHistogramSamples` to encode custom-bucket integer histograms, appending `CustomValues` as a uvarint count followed by big-endian float64 values.
    *   Implement `Encoder.CustomBucketsFloatHistogramSamples` for custom-bucket float histograms with the same `CustomValues` encoding.

*   Update decoding methods:
    *   Modify `Decoder.HistogramSamples` to decode both standard and custom-bucket integer histogram records, populating `CustomValues`.
    *   Modify `Decoder.FloatHistogramSamples` for float histograms, ensuring `CustomValues` are populated.
    *   Update `Decoder.Type` to recognize new custom-bucket record types and return the correct constants.

*   Ensure consistent handling across all WAL consumers:
    *   During head reload, agent WAL replay, checkpoint creation, out-of-order buffer replay, and WAL watcher tailing, process custom-bucket records identically to standard records.
    *   During WAL checkpoint creation, filter and re-encode custom-bucket histograms using the appropriate encoder methods.
    *   Ensure the WAL watcher counts custom-bucket histograms in the same counter as standard histograms.

*   Add test utility functions:
    *   Implement `GenerateTestCustomBucketsHistograms` to create a specified number of custom-bucket integer histograms.
    *   Implement `GenerateTestCustomBucketsFloatHistograms` for float histograms, with similar behavior.

*   Handle appending and out-of-order processing:
    *   Register series for custom-bucket histograms in the WAL and write them as custom-bucket records.
    *   Ensure out-of-order WAL writes process custom-bucket records correctly, maintaining counter-reset hint tracking.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.