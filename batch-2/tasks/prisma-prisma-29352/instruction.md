I'm building out a new CLI command to help developers connect their local projects to a hosted Postgres database service.

*   PostgresCommand.new() must accept an optional map of subcommand overrides. When parsed with no arguments or with --help, the output must contain 'prisma postgres' and 'link'. When an unknown subcommand is provided, parse() must return an Error instance.

*   PostgresCommand must dispatch to a registered subcommand by name, forwarding only the remaining arguments (after the subcommand name), the config object, and the cwd to the subcommand's parse() method, and returning the subcommand's result.

*   Link.new().parse() must return help text containing 'prisma postgres link' when called with --help.

*   Link.parse() must return a HelpError whose message contains '--database' when --api-key is provided without --database.

*   Link.parse() must validate that the --database value matches the 'db_' prefix format and return a HelpError whose message contains 'db_' when the format is invalid.

*   In non-interactive mode (both --api-key and --database provided), Link.parse() must call the management API to create a connection, write the direct DATABASE_URL to the .env file in the working directory using single quotes (e.g. DATABASE_URL='<url>'), and return a string containing 'linked successfully'.

*   When --api-key is omitted but the PRISMA_API_KEY environment variable is set and --database is also provided, Link must read the API key from PRISMA_API_KEY and call createManagementApiClient with an object containing { token: <value> }.

*   When the linked schema already contains data models, the success output must include 'prisma generate' and 'prisma migrate dev' as suggested next steps.

*   When the schema has no data models (or no schema file exists), the success output must include 'Define your data model' as a suggested next step.

*   When the management API returns an error (e.g. unauthorized), Link.parse() must return a HelpError whose message contains 'Invalid credentials'.

*   If the .env file already contains a DATABASE_URL pointing to a Prisma Postgres host (db.prisma.io or db-pool.prisma.io), Link.parse() must skip the API call and return a string containing 'already linked' and '--force'. When --force is added, the command must re-link regardless and call the API.

*   In interactive mode (no --api-key and no --database), Link must use browser-based authentication (createAuthenticatedManagementAPI) even if PRISMA_API_KEY is set, and must NOT call createManagementApiClient. It must prompt the user to 'Select a project:' then 'Select a database:'. When only one database exists, it must auto-select without prompting for the database.

*   In interactive mode, if the API returns no projects, Link.parse() must return a HelpError whose message contains 'No projects found'. If no databases have status 'ready', it must return a HelpError whose message contains 'No ready databases'.

*   When an authentication error with an invalid refresh token occurs, Link must retry by calling login() with an options object containing { utmMedium: 'command-postgres-link' }.

*   upsertEnvFile(envPath, vars) must create the .env file if it does not exist (returned.created === true, returned.added lists the keys, returned.updated is empty). It must append new keys to an existing file (returned.created === false) and update existing keys in-place. Values must be written with single quotes to prevent variable expansion. The exact format per line is KEY='value'\n.

*   checkGitignore(dir) must return 'no-file' when .gitignore is absent, 'ok' when .gitignore contains '.env' or '/.env', and 'missing-entry' when .gitignore exists but does not include .env.

*   writeLocalFiles(dir, connection) must write DATABASE_URL from connection.connectionString to the .env file and return { env: { created, added, updated } }.

*   isAlreadyLinked(dir) must return false when no .env exists, false when DATABASE_URL does not point to a Prisma Postgres host, true when DATABASE_URL contains 'db.prisma.io', and true when it contains 'db-pool.prisma.io'.

*   formatEnvSummary({ env, gitignoreStatus }) must return exactly '  Created .env with connection strings' when env.created is true. When gitignoreStatus is 'missing-entry', it must append exactly '\n    warn Your .gitignore does not include .env — add it to avoid committing secrets'. When gitignoreStatus is 'no-file', it must append exactly '\n    warn No .gitignore found — create one and add .env to avoid committing secrets'.

*   createDevConnection(client, databaseId) must return a ConnectionResult preferring endpoints.direct.connectionString, falling back to endpoints.pooled.connectionString, then the deprecated top-level connectionString field. It must throw with a message matching /No connection string found/ when none are present. It must throw with a message matching /Invalid credentials/ on authentication-failed errors, /not found/ on not_found errors, and the original API error message for generic errors.

*   listProjects(client) must return an array of project objects from the API response. listDatabases(client, projectId) must return an array of database objects for the given project. Both must throw with the API error message on failure.

*   sanitizeErrorMessage(message) must replace postgres:// and prisma+postgres:// connection strings with '[REDACTED_URL]', and replace --api-key flag values with '--api-key [REDACTED]'.


*   Interface details: Type: Class
Name: PostgresCommand
Location: packages/cli/src/postgres/PostgresCommand.ts
Description: Top-level CLI command for Prisma Postgres operations. Dispatches to registered subcommands.
Signature:
  static new(subcommands: Record<string, { parse: (args: string[], config: unknown, cwd: string) => Promise<string | Error> }>): PostgresCommand
  parse(args: string[], config: unknown, cwd: string): Promise<string | Error>

Type: Class
Name: Link
Location: packages/cli/src/postgres/link/Link.ts
Description: CLI subcommand that links a local project to a Prisma Postgres database by writing connection strings to the local environment file.
Signature:
  static new(): Link
  parse(args: string[], config: unknown, cwd: string): Promise<string | import('@prisma/internals').HelpError | Error>

Type: Function
Name: upsertEnvFile
Location: packages/cli/src/postgres/link/local-setup.ts
Signature: upsertEnvFile(envPath: string, vars: Record<string, string>): { created: boolean; added: string[]; updated: string[] }
Description: Creates or updates a .env file with the given key-value pairs. Values are written with single quotes. Returns metadata about which keys were created, added, or updated.

Type: Function
Name: checkGitignore
Location: packages/cli/src/postgres/link/local-setup.ts
Signature: checkGitignore(dir: string): 'no-file' | 'ok' | 'missing-entry'
Description: Checks whether a .gitignore file exists in the given directory and whether it includes an entry for .env. Returns 'no-file' if .gitignore is absent, 'ok' if .env or /.env is present, 'missing-entry' if .gitignore exists but does not include .env.

Type: Function
Name: writeLocalFiles
Location: packages/cli/src/postgres/link/local-setup.ts
Signature: writeLocalFiles(dir: string, connection: ConnectionResult): { env: { created: boolean; added: string[]; updated: string[] } }
Description: Writes DATABASE_URL from the given ConnectionResult to the .env file in the specified directory.

Type: Function
Name: isAlreadyLinked
Location: packages/cli/src/postgres/link/local-setup.ts
Signature: isAlreadyLinked(dir: string): boolean
Description: Returns true if the .env file in the given directory contains a DATABASE_URL pointing to a Prisma Postgres host (db.prisma.io or db-pool.prisma.io). Returns false if no .env exists, DATABASE_URL is absent, or it points to a non-Prisma host.

Type: Function
Name: formatEnvSummary
Location: packages/cli/src/postgres/link/local-setup.ts
Signature: formatEnvSummary(opts: { env: { created: boolean; added: string[]; updated: string[] }; gitignoreStatus: 'no-file' | 'ok' | 'missing-entry' }): string
Description: Formats a human-readable summary string about the .env file operation. Exact output:
  - When env.created is true: `"  Created .env with connection strings"`
  - When gitignoreStatus is 'missing-entry': appends `"\n    warn Your .gitignore does not include .env — add it to avoid committing secrets"`
  - When gitignoreStatus is 'no-file': appends `"\n    warn No .gitignore found — create one and add .env to avoid committing secrets"`

Type: Function
Name: createDevConnection
Location: packages/cli/src/postgres/link/management-api.ts
Signature: createDevConnection(client: ApiClient, databaseId: string): Promise<ConnectionResult>
Description: Calls the management API to create a dev connection for the given database. Returns a ConnectionResult preferring the direct endpoint, falling back to the pooled endpoint, then to a deprecated top-level connectionString field. Throws on API errors:
  - Authentication-failed error: throws with message matching /Invalid credentials/
  - Not-found error: throws with message matching /not found/
  - Missing connection string in response: throws with message matching /No connection string found/
  - Generic API error: throws with the original error message

Type: Function
Name: listProjects
Location: packages/cli/src/postgres/link/management-api.ts
Signature: listProjects(client: ApiClient): Promise<Project[]>
Description: Returns the list of projects from the management API. Throws with the API error message on failure.

Type: Function
Name: listDatabases
Location: packages/cli/src/postgres/link/management-api.ts
Signature: listDatabases(client: ApiClient, projectId: string): Promise<Database[]>
Description: Returns the list of databases for a given project from the management API. Throws with the API error message on failure.

Type: Function
Name: sanitizeErrorMessage
Location: packages/cli/src/postgres/link/management-api.ts
Signature: sanitizeErrorMessage(message: string): string
Description: Removes sensitive data from error messages. Replaces postgres:// and prisma+postgres:// connection strings with [REDACTED_URL]. Replaces --api-key flag values with --api-key [REDACTED].

Type: Interface/Type
Name: ConnectionResult
Location: packages/cli/src/postgres/link/management-api.ts
Description: Result type returned by createDevConnection. Has shape: { connectionString: string }


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.