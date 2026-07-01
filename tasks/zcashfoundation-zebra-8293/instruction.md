Implement input validation for key-management operations in the gRPC scanner service to ensure requests are processed correctly. Reject requests with zero keys or more keys than the defined maximum, and return appropriate error messages.

*   Declare a public constant:
    *   Name: `MAX_KEYS_PER_REQUEST`
    *   Location: `zebra-grpc/src/server.rs`
    *   Type: `usize`
    *   Value: No greater than 10

*   Update gRPC methods to validate key list:
    *   `get_results` method:
        *   Reject requests with an empty keys list by returning a `tonic::Status::invalid_argument` error.
        *   Reject requests with a keys list length exceeding `MAX_KEYS_PER_REQUEST` by returning a `tonic::Status::invalid_argument` error.
    *   `register_keys` method:
        *   Reject requests with an empty keys list by returning a `tonic::Status::invalid_argument` error.
        *   Reject requests with more than `MAX_KEYS_PER_REQUEST` keys by returning a `tonic::Status::invalid_argument` error.
    *   `clear_results` method:
        *   Reject requests with an empty keys list by returning a `tonic::Status::invalid_argument` error.
        *   Reject requests with more than `MAX_KEYS_PER_REQUEST` keys by returning a `tonic::Status::invalid_argument` error.
    *   `delete_keys` method:
        *   Reject requests with an empty keys list by returning a `tonic::Status::invalid_argument` error.
        *   Reject requests with more than `MAX_KEYS_PER_REQUEST` keys by returning a `tonic::Status::invalid_argument` error.

*   Ensure correct behavior for valid requests:
    *   `get_results` with valid keys:
        *   Return a results map with a `by_height` map containing 3 entries on Mainnet and 1 entry on Testnet if scan results are populated.
        *   Return a results map with an empty `by_height` map if scan results are empty.
    *   `register_keys` with valid keys:
        *   Response must include a `keys` field (`Vec<String>`) matching the list returned by the scan service.
    *   `clear_results` with valid keys:
        *   Response must be an empty `Empty` message.
    *   `delete_keys` with valid keys:
        *   Response must be an empty `Empty` message.
    *   `get_info` method:
        *   Response `InfoReply` must have a `min_sapling_birthday_height` field equal to the Sapling activation height for the current network.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.