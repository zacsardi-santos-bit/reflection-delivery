Implement structured error reporting for transaction execution in the cryptocurrency service. Ensure that the blockchain explorer API provides clear success or failure status for transactions, and that wallet API endpoints handle errors correctly.

*   Update the `TxTransfer` transaction execute method:
    *   Return a structured error with code 1 if the sender wallet does not exist.
    *   Return a structured error with code 2 if the receiver wallet does not exist.
    *   Return a structured error with code 3 if the sender has insufficient balance.
*   Modify the blockchain explorer API:
    *   Ensure the endpoint `v1/transactions/{tx_hash}` returns a JSON object with a 'status' field.
    *   For successful transactions, set the 'status' field to `{"type": "success"}`.
    *   For failed transactions, set the 'status' field to `{"type": "error", "code": N, "description": ""}`, where N is the specific error code (1, 2, or 3).
*   Adjust the wallet API endpoints:
    *   The GET `v1/wallet/{pub_key}` endpoint must return an error string starting with 'Invalid request param' for malformed public keys.
    *   Return 'Wallet not found' for valid public keys with no corresponding wallet on the blockchain.
*   Define shared constants in the module `examples/cryptocurrency/tests/constants/mod.rs`:
    *   `pub const ALICE_NAME: &str = "Alice";`
    *   `pub const BOB_NAME: &str = "Bob";`
*   Implement the `assert_tx_status` function in `examples/cryptocurrency/tests/api.rs`:
    *   Query the blockchain explorer at `ApiKind::Explorer / "v1/transactions/{tx_hash}"`.
    *   Extract and verify the "status" field against `expected_status`.
    *   Panic with "Invalid transaction info format, object expected" if the response is not a JSON object.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.