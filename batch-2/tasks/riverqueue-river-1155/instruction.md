I'm trying to run River behind a connection pooler configured in transaction pooling mode.

*   A new unexported function `adaptArgsForJSONTextModes` must be added to the `riverpgxv5` package with signature `adaptArgsForJSONTextModes(defaultMode pgx.QueryExecMode, sql string, args []any) []any`. It returns an updated args slice with JSON adaptations applied when the effective execution mode is simple-protocol or exec mode.

*   When the effective mode is `pgx.QueryExecModeSimpleProtocol` or `pgx.QueryExecModeExec`, `adaptArgsForJSONTextModes` must convert any `[]byte` argument bound to a `::json` or `::jsonb` SQL placeholder into `json.RawMessage`, and convert any `[][]byte` argument bound to a `::json[]` or `::jsonb[]` placeholder into `[]json.RawMessage`.

*   When the effective mode is `pgx.QueryExecModeCacheStatement`, `adaptArgsForJSONTextModes` must return the args slice unchanged — no type conversions should be applied.

*   Arguments explicitly bound to `::bytea`, `::bytea[]`, or `CAST($n AS bytea)` SQL casts must be preserved as their original `[]byte` / `[][]byte` types, regardless of execution mode.

*   A nil `[][]byte` argument that is otherwise eligible for conversion must be converted to a nil `[]json.RawMessage` (not left as `[][]byte`).

*   When the args slice contains a `pgx.QueryExecMode` value as a leading query option (before the first bind argument), that value must override the `defaultMode` parameter when determining whether to adapt args.

*   Leading query option args such as `pgx.QueryResultFormats` / `pgx.QueryResultFormatsByOID` and `pgx.QueryExecMode` must be skipped when matching positional arguments against SQL placeholder indices, so that bind arg indices align correctly with `$1`, `$2`, etc.

*   When the args slice contains a `pgx.QueryRewriter` as a leading option, `adaptArgsForJSONTextModes` must wrap it in a new `pgx.QueryRewriter` that applies JSON adaptation to the final SQL and args after the inner rewriter runs. The original bind args must not be changed before the rewrite.

*   The `templateReplaceWrapper` struct in `riverdriver/riverpgxv5/river_pgx_v5_driver.go` must have a new method `defaultQueryExecMode() pgx.QueryExecMode`. When the underlying `dbtx`'s `Conn()` method returns nil, `defaultQueryExecMode()` must return `pgx.QueryExecModeCacheStatement`. When `Conn()` panics unexpectedly, the panic must propagate (not be swallowed).

*   The `Exec`, `Query`, and `QueryRow` methods on `templateReplaceWrapper` must call `adaptArgsForJSONTextModes` (with the result of `defaultQueryExecMode()` as the default mode) on the args before forwarding the call to the underlying `dbtx`.

*   The `Conn()` method on `SharedTx` in `internal/riverinternaltest/sharedtx/shared_tx.go` must return `nil` instead of panicking, so that callers performing capability probes through `Conn()` can handle the nil case safely.


*   Interface details: Type: Function
Name: adaptArgsForJSONTextModes
Location: riverdriver/riverpgxv5/ (new file, e.g., json_text_mode_adaptation.go)
Signature: adaptArgsForJSONTextModes(defaultMode pgx.QueryExecMode, sql string, args []any) []any
Description: Adapts the provided query arguments for text-based pgx execution modes (simple protocol and exec). For each positional argument bound to a ::json or ::jsonb SQL cast, converts []byte to json.RawMessage and [][]byte to []json.RawMessage. Arguments bound to ::bytea or CAST($n AS bytea) are preserved unchanged. In cache-statement mode the args are returned as-is. A pgx.QueryExecMode found as a leading query option in args overrides the defaultMode parameter. A pgx.QueryRewriter found in args is wrapped so JSON adaptation runs after the rewrite.

Type: Method
Name: defaultQueryExecMode
Location: riverdriver/riverpgxv5/river_pgx_v5_driver.go
Signature: (w templateReplaceWrapper) defaultQueryExecMode() pgx.QueryExecMode
Description: Returns the configured default query execution mode for the connection underlying the templateReplaceWrapper. Checks whether dbtx exposes a pool config (via Config() *pgxpool.Config) or a connection (via Conn() *pgx.Conn) and reads DefaultQueryExecMode from it. Returns pgx.QueryExecModeCacheStatement when mode cannot be determined (e.g., Conn() returns nil). If Conn() panics unexpectedly, the panic propagates.

Type: Struct (existing, modified)
Name: templateReplaceWrapper
Location: riverdriver/riverpgxv5/river_pgx_v5_driver.go
Description: Existing struct that wraps a pgx DBTX with SQL template replacement. Its Exec, Query, and QueryRow methods must be updated to call adaptArgsForJSONTextModes (using defaultQueryExecMode() as the default mode) before forwarding to the underlying dbtx. The struct has unexported fields: dbtx (the underlying DBTX) and replacer (*sqlctemplate.Replacer).

Type: Method (existing, modified)
Name: Conn
Location: internal/riverinternaltest/sharedtx/shared_tx.go
Signature: (e *SharedTx) Conn() *pgx.Conn
Description: Must return nil instead of panicking. SharedTx does not expose a stable underlying connection pointer; nil is the correct signal for callers probing capabilities through Conn().


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.