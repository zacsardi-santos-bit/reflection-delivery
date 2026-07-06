I'm working on adding data catalog safety checks to the R2 CLI commands.

*   The 'r2 object put' command must accept a '--force' flag (with '-y' as an alias) that bypasses data catalog confirmation checks.

*   When 'r2 object put' is invoked WITHOUT '--force'/'-y', it must include a 'cf-r2-data-catalog-check: true' HTTP header in the PUT request to the R2 objects API endpoint.

*   When 'r2 object put' is invoked WITH '--force' or '-y', it must NOT send the 'cf-r2-data-catalog-check' header in the PUT request.

*   When the R2 API returns HTTP 409 with error code 10081 for an 'r2 object put' request, the command must prompt the user with the exact text: 'Data catalog is enabled for this bucket. Proceeding may leave the data catalog in an invalid state. Continue?' If the user confirms, the command must retry the PUT request without the 'cf-r2-data-catalog-check' header (making 2 total requests). If the user declines, the command must output 'Operation cancelled.' and stop.

*   The 'r2 object delete' command must accept a '--force' flag (with '-y' as an alias). Without '--force'/'-y', it must send 'cf-r2-data-catalog-check: true' in the DELETE request. With '--force'/'-y', it must NOT send the header. On HTTP 409 with error code 10081, same prompt/retry/cancel behavior as 'r2 object put'.

*   The 'r2 bulk put' command must accept a '--force' flag (with '-y' as an alias). Without '--force'/'-y', it must display an upfront prompt before any uploads with the exact text: 'Bulk upload may overwrite existing objects. If this bucket has data catalog enabled, this operation could leave the catalog in an invalid state. Continue?'

*   If the user confirms the 'r2 bulk put' upfront prompt, the bulk upload must proceed and must NOT send the 'cf-r2-data-catalog-check' header on any individual upload requests.

*   If the user declines the 'r2 bulk put' upfront prompt, the command must perform zero uploads and output 'Bulk upload cancelled.'

*   When 'r2 bulk put' is invoked WITH '--force' or '-y', no upfront prompt is shown and no 'cf-r2-data-catalog-check' header is sent.

*   In non-interactive contexts, the 'r2 bulk put' upfront confirmation prompt must output the prompt text followed by '🤖 Using fallback value in non-interactive context: yes' and proceed with the upload.

*   The 'r2 bucket lifecycle add' command must accept a '--force' flag (with '-y' as an alias). Without '--force'/'-y', it must send 'cf-r2-data-catalog-check: true' in the lifecycle PUT request. With '--force'/'-y', it must NOT send the header. On HTTP 409 with error code 10081, prompt 'Data catalog is enabled for this bucket. Proceeding may leave the data catalog in an invalid state. Continue?' — retry without header if confirmed, output 'Operation cancelled.' if declined.

*   The 'r2 bucket lifecycle set' command must accept a '--force' flag (with '-y' as an alias). Without '--force'/'-y', it must send 'cf-r2-data-catalog-check: true' in the lifecycle PUT request. With '--force'/'-y', it must NOT send the header. Same 409 prompt/retry/cancel behavior.

*   The 'r2 bucket lifecycle remove' command must NOT send the 'cf-r2-data-catalog-check' header, as lifecycle removal operations are not relevant to catalog state.


*   Interface details: NO INTERFACES NEEDED

The tests exercise existing wrangler CLI commands (r2 object put, r2 object delete, r2 bulk put, r2 bucket lifecycle add, r2 bucket lifecycle set, r2 bucket lifecycle remove) via the runWrangler test helper. No new exported functions or classes need to be created; the implementation changes are internal to the existing CLI command handlers.

The key observable interface points are:

1. All of the following commands must now accept a `--force` flag with `-y` as an alias:
   - `r2 object put`
   - `r2 object delete`
   - `r2 bulk put`
   - `r2 bucket lifecycle add`
   - `r2 bucket lifecycle set`

2. The HTTP header `cf-r2-data-catalog-check` with value `"true"` must be sent on write requests when `--force`/`-y` is NOT provided.

3. The exact prompt/output strings the tests assert:
   - Upfront bulk put prompt: `"Bulk upload may overwrite existing objects. If this bucket has data catalog enabled, this operation could leave the catalog in an invalid state. Continue?"`
   - Per-operation 409 conflict prompt: `"Data catalog is enabled for this bucket. Proceeding may leave the data catalog in an invalid state. Continue?"`
   - Cancellation message (object/lifecycle): `"Operation cancelled."`
   - Cancellation message (bulk put): `"Bulk upload cancelled."`
   - Non-interactive fallback message: `"🤖 Using fallback value in non-interactive context: yes"`

4. The API error code that triggers the conflict prompt is `10081`, returned with HTTP status `409`.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.