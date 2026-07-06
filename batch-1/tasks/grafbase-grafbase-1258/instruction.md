Fix the federated GraphQL gateway's error handling so that when an upstream mutation returns null data with a non-empty errors list, only the upstream errors are forwarded to the client. Suppress any internally-generated "missing required field" errors in such cases.

*   Ensure that when the gateway receives a response with null data and a non-empty errors list from an upstream subgraph:
    *   Only the upstream error(s) are included in the response to the client.
    *   Format each upstream error message as 'Upstream error: <original message>' and attach the correct response path.
    *   Suppress any internally-generated 'missing required field' errors that occur during deserialization of null data.
*   Maintain the response data field as null when the upstream subgraph returns null data, irrespective of error handling.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.