Update the `DenomOwners` gRPC query handler to return an error and nil response for all requests, regardless of the token denomination or pagination parameters. Ensure that the query consistently indicates the feature is unavailable due to the absence of the required reverse index.

*   Modify the `DenomOwners` function in `x/bank/keeper/grpc_query.go`:
    *   Ensure it returns a non-nil error and a nil response for all non-nil requests.
    *   The error message should clearly indicate that the query is unavailable in this deployment.
*   Ensure the function signature remains:
    *   `func (k BaseKeeper) DenomOwners(goCtx context.Context, req *types.QueryDenomOwnersRequest) (*types.QueryDenomOwnersResponse, error)`
*   Implement consistent behavior for the following scenarios:
    *   Requests with valid denom strings and no funded addresses should return an error and nil response.
    *   Requests with valid denom strings and multiple funded accounts across multiple pages should return an error and nil response.
    *   Requests with any valid denom and any pagination parameters should always result in an error and nil response.
*   Ensure the error response is uniform across all valid and invalid denom requests, emphasizing the unavailability of the feature.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.