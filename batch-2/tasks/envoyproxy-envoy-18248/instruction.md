Update the Kafka mesh proxy filter to ensure message headers are preserved and forwarded to upstream Kafka brokers. Implement header management and error handling to maintain message integrity and resource efficiency. Follow the outlined requirements to modify the necessary components and interfaces.

*   Modify the `OutboundRecord` struct:
    *   Add a `headers` field as the 5th member, of type `std::vector<Header>`.
    *   Update constructor signature to: `OutboundRecord(const std::string& topic, const int32_t partition, const absl::string_view key, const absl::string_view value, const std::vector<Header>& headers)`.
    *   Ensure initialization order is: `{topic, partition, key, value, headers}`.

*   Update the `KafkaProducer` interface:
    *   Change the `send` method signature to: `void send(const ProduceFinishCbSharedPtr callback, const OutboundRecord& record)`.

*   Implement changes in `RichKafkaProducer`:
    *   Update the `send` method to match the new `KafkaProducer` interface signature.

*   Extend the `LibRdKafkaUtils` interface:
    *   Add `convertHeaders` method: `RdKafka::Headers* convertHeaders(const std::vector<std::pair<absl::string_view, absl::string_view>>& headers) const`.
    *   Add `deleteHeaders` method: `void deleteHeaders(RdKafka::Headers* librdkafka_headers) const`.

*   Implement error handling for header conversion and message sending:
    *   Abort the send operation if `convertHeaders` returns `nullptr` and notify the failure using `ProduceFinishCb`.
    *   Avoid calling `deleteHeaders` if `convertHeaders` fails.
    *   Call `deleteHeaders` if the produce operation fails after successful header conversion.
    *   Do not call `deleteHeaders` if produce succeeds, as ownership of headers is transferred.

*   Ensure the underlying produce call includes headers, increasing the argument count to 10.

*   Verify end-to-end message integrity:
    *   Ensure messages forwarded through the proxy preserve the original key, value, and headers.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.