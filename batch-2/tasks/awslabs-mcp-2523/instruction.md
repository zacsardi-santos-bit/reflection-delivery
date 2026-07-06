I'm working with an AWS HealthOmics MCP server and I need to add tools for managing run caches.

*   The CACHE_BEHAVIORS constant must be a list containing exactly 'CACHE_ALWAYS' and 'CACHE_ON_FAILURE'.

*   create_run_cache must validate the cache_behavior parameter against CACHE_BEHAVIORS; if not valid, it must call handle_tool_error with a ValueError and return a dict containing an 'error' key without calling the HealthOmics create API.

*   create_run_cache must validate that cache_s3_location is a properly formatted S3 URI (starting with 's3://' followed by a non-empty bucket name); malformed URIs must return an error dict without calling the HealthOmics create API.

*   create_run_cache must obtain an S3 client and call head_bucket(Bucket=<bucket_name>) to verify bucket accessibility before calling the HealthOmics API. The bucket name is extracted from the S3 URI. Any S3 failure must prevent the HealthOmics create API from being called.

*   When head_bucket returns a 404 error, create_run_cache must pass a ValueError whose message contains 'does not exist' to handle_tool_error. When head_bucket returns a 403 error, the ValueError message must contain 'Access denied'. For any other S3 error code, the function must return {'error': '...'} where the error string contains 'Error accessing S3 bucket' and the bucket name.

*   When all validations pass, create_run_cache must call client.create_run_cache with keyword arguments: requestId (a valid UUID v4 string), cacheBehavior (equal to cache_behavior), cacheS3Location (equal to cache_s3_location), and only the optional parameters actually provided. Parameter mapping: cache_bucket_owner_id -> cacheBucketOwnerId. No None values or unprovided parameters may be included.

*   create_run_cache must return the dict returned by the HealthOmics API (containing at minimum 'id', 'arn', 'status'). On any exception from the HealthOmics API, it must return {'error': '<message>'} where the message contains the exception's string representation.

*   get_run_cache must call client.get_run_cache(id=cache_id) and return all response fields. Any datetime fields must be converted to ISO 8601 strings using .isoformat(). Non-datetime fields must be preserved exactly as returned. On exception, return {'error': '<message>'} where message contains the exception string.

*   list_run_caches must call client.list_run_caches with maxResults always present. Optional filter parameters are forwarded only when provided: name -> name, status -> status, cache_behavior -> cacheBehavior, next_token -> startingToken. No other parameters may be included beyond what was explicitly provided plus maxResults.

*   list_run_caches must return {'runCaches': [...]} where the list corresponds to the 'items' from the API response. The 'nextToken' key must be present in the output if and only if the API response contained a 'nextToken'. On exception, return {'error': '<message>'}.

*   update_run_cache must call client.update_run_cache with id=cache_id always present. Optional parameters forwarded only when provided: cache_behavior -> cacheBehavior, name -> name, description -> description. No extra parameters may be included. Must return {'id': cache_id, 'status': 'updated'}. On exception, return {'error': '<message>'} where message contains the exception string.


*   Interface details: Type: Constant
Name: CACHE_BEHAVIORS
Location: src/aws-healthomics-mcp-server/awslabs/aws_healthomics_mcp_server/consts.py
Description: List of valid cache behavior values. Must contain exactly 'CACHE_ALWAYS' and 'CACHE_ON_FAILURE'.

Type: Function
Name: create_run_cache
Location: src/aws-healthomics-mcp-server/awslabs/aws_healthomics_mcp_server/tools/run_cache.py
Signature: create_run_cache(ctx, cache_behavior: str, cache_s3_location: str, name: Optional[str] = None, description: Optional[str] = None, tags: Optional[dict] = None, cache_bucket_owner_id: Optional[str] = None) -> dict
Description: Creates a new HealthOmics run cache. Validates cache_behavior against CACHE_BEHAVIORS (raises ValueError and calls handle_tool_error on invalid). Validates cache_s3_location is a valid S3 URI with a non-empty bucket name. Calls head_bucket on the S3 bucket before creating the cache; a 404 response raises a ValueError containing "does not exist", a 403 response raises a ValueError containing "Access denied", and any other error returns {'error': 'Error accessing S3 bucket ... <bucket_name> ...'}. On a valid S3 URI with accessible bucket, calls client.create_run_cache(requestId=<uuid4_string>, cacheBehavior=cache_behavior, cacheS3Location=cache_s3_location, [name, description, tags, cacheBucketOwnerId only when provided]). The requestId must be a valid UUID string. Returns the API response dict (with 'id', 'arn', 'status'). On any exception, returns {'error': '<exception_message>'}.

Type: Function
Name: get_run_cache
Location: src/aws-healthomics-mcp-server/awslabs/aws_healthomics_mcp_server/tools/run_cache.py
Signature: get_run_cache(ctx, cache_id: str) -> dict
Description: Retrieves details of a specific run cache. Calls client.get_run_cache(id=cache_id). Returns all fields from the API response with datetime values serialized to ISO 8601 strings (using .isoformat()). Non-datetime fields are returned as-is. On exception, returns {'error': '<exception_message>'}.

Type: Function
Name: list_run_caches
Location: src/aws-healthomics-mcp-server/awslabs/aws_healthomics_mcp_server/tools/run_cache.py
Signature: list_run_caches(ctx, name: Optional[str] = None, status: Optional[str] = None, cache_behavior: Optional[str] = None, next_token: Optional[str] = None) -> dict
Description: Lists run caches with optional filtering. Calls client.list_run_caches(maxResults=<value>, [name, status, cacheBehavior, startingToken only when provided]). Parameter mapping: cache_behavior -> cacheBehavior, next_token -> startingToken. maxResults is always included. Returns {'runCaches': [...items...]} optionally with 'nextToken' key only when present in the API response. On exception, returns {'error': '<exception_message>'}.

Type: Function
Name: update_run_cache
Location: src/aws-healthomics-mcp-server/awslabs/aws_healthomics_mcp_server/tools/run_cache.py
Signature: update_run_cache(ctx, cache_id: str, cache_behavior: Optional[str] = None, name: Optional[str] = None, description: Optional[str] = None) -> dict
Description: Updates an existing run cache. Calls client.update_run_cache(id=cache_id, [cacheBehavior, name, description only when provided]). Parameter mapping: cache_behavior -> cacheBehavior. No extra keys are forwarded beyond what was provided. Returns {'id': cache_id, 'status': 'updated'}. On exception, returns {'error': '<exception_message>'}.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.