I'm working on a CLI tool that manages containerized workloads, and I need to improve the command that lists containers.

*   The containers list command must fetch data from the /dash/applications API endpoint (not the legacy /applications endpoint).

*   The containers list command help must expose two new options: --per-page (Number of containers per page, type: number, default: 25) and --json (Return output as JSON, type: boolean, default: false).

*   For both the containers list and containers instances commands, passing --per-page with a value of 0 or any negative integer must throw an error with a message matching '--per-page must be at least 1'.

*   When running containers list in non-TTY (non-interactive) mode without --json, the command must render a formatted table with exactly these column headers: ID, NAME, STATE, LIVE INSTANCES, LAST MODIFIED.

*   When running containers list in non-TTY mode without --json and no containers are returned, the command must output exactly: 'No containers found.'

*   When running containers list in non-TTY mode (or with --json), the API request must be made without per_page or page_token query parameters — a single unpaginated request.

*   Container state must be derived from the health.instances counters using this exact priority: if failed > 0, the state is 'degraded'; else if starting > 0 or scheduling > 0, the state is 'provisioning'; else if active > 0, the state is 'active'; otherwise the state is 'ready'.

*   When the /dash/applications API returns a 400 HTTP status, containers list must throw an error with a message matching 'There has been an error listing containers'.

*   When the /dash/applications API returns a 500 HTTP status, containers list must throw an error with a message matching 'unknown error listing containers'.

*   With --json, the containers list command must output a JSON array where each entry has exactly these fields: id (string), name (string), state (string), instances (number), image (string), version (number), updated_at (string), created_at (string).

*   With --json and no containers returned, the containers list command must output an empty JSON array.

*   When the user lacks the required scope, containers list must throw an error containing: "You need 'containers:write', try logging in again or creating an appropiate API token".

*   In non-TTY mode without --json, the containers instances command must render a formatted table instead of JSON. For standard (non-DO) apps the columns are: INSTANCE, STATE, LOCATION, VERSION, CREATED. For DO-backed apps the columns are: INSTANCE, NAME, STATE, LOCATION, VERSION, CREATED, with null values displayed as '-'.

*   The MOCK_DASH_APPLICATIONS constant must be exported from the mock-cloudchamber test helper and contain exactly 4 DashApplication entries representing all four derived states: active, degraded, provisioning, and ready.

*   The DashApplication type must be exported from the @cloudflare/containers-shared package and must include the fields: id, created_at, updated_at, name, version, instances, image, and health.instances (with sub-fields: active, healthy, failed, starting, scheduling — all numbers).


*   Interface details: Type: Constant
Name: MOCK_DASH_APPLICATIONS
Location: packages/wrangler/src/__tests__/helpers/mock-cloudchamber.ts
Signature: MOCK_DASH_APPLICATIONS: DashApplication[]
Description: Exported array of 4 mock DashApplication objects covering all four derived states: active, degraded, provisioning, and ready. Each object must include: id (string), created_at (string), updated_at (string), name (string), version (number), instances (number), image (string), and health.instances with sub-fields active, healthy, failed, starting, scheduling (all numbers).

Type: Type
Name: DashApplication
Location: packages/containers-shared/src/client/models/DashApplication.ts
Description: Exported TypeScript type (also re-exported from the @cloudflare/containers-shared package root) representing a container application as returned by the /dash/applications endpoint. Must have exactly these fields: id (ApplicationID / string), created_at (ISO8601Timestamp / string), updated_at (ISO8601Timestamp / string), name (ApplicationName / string), version (number), instances (number), image (Image / string), health (ApplicationHealth — containing an instances sub-object with numeric fields: active, healthy, failed, starting, scheduling).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.