Implement the functionality to manage local retention configuration for tiered-storage Kafka topics in the Terraform provider. Ensure that the relevant fields are correctly processed and validated, and that the API responses are accurately reflected in the Terraform state.

*   Export the `FlattenKafkaTopicConfig` function in the `kafkatopic` package located at `internal/sdkprovider/service/kafkatopic/kafka_topic.go`.
    *   Use the signature: `FlattenKafkaTopicConfig(t *aiven.KafkaTopic) ([]map[string]interface{}, error)`.
    *   Ensure it returns only configuration fields that are non-nil in the API response.
    *   Convert integer-valued config fields that are typed as strings in the schema to string representations in the output map.
    *   Return boolean config fields as actual boolean values.
    *   Return float config fields as actual float values.
    *   Return a non-nil error if the topic config cannot be marshaled/processed; otherwise, return nil.

*   Update the Kafka topic resource to validate configurations:
    *   Reject configurations where `local_retention_bytes` is set without `retention_bytes` with the error: "local_retention_bytes can't be set without retention_bytes".
    *   Reject configurations where `local_retention_bytes` is greater than `retention_bytes` with the error: "local_retention_bytes must not be more than retention_bytes value".

*   Ensure `local_retention_bytes` and `local_retention_ms` fields in the Kafka topic resource config are functional:
    *   Send their values to the Kafka API on create/update.
    *   Read their values back from the API into the Terraform state.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.