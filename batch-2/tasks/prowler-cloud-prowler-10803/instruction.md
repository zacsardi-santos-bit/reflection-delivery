I'm working on the findings section of our security dashboard and I've run into a few related problems that I'd like to clean up together.

*   A new dedicated sort-constants module must be created at `ui/lib/findings-sort.ts` and re-exported through the `@/lib` barrel so all existing action and component code can access it without importing server-only authentication code.

*   The sort module must define two distinct families of sort tokens: Family A for plain findings (where ascending Postgres ENUM order naturally produces FAIL/critical first) and Family B for finding-groups (where integer-weighted computed columns require descending order to put FAIL/critical/new results first). Family A tokens must never carry a minus prefix for `status` or `severity`. Family B tokens must always carry a minus prefix for `status`, `severity`, and `delta`.

*   Family A primitive tokens must have these exact values: `FINDINGS_FAIL_FIRST = "status"`, `FINDINGS_SEVERITY_HIGH_FIRST = "severity"`, `FINDINGS_RECENT_INSERT = "-inserted_at"`, `FINDINGS_RECENT_UPDATE = "-updated_at"`.

*   Family B primitive tokens must have these exact values: `FG_FAIL_FIRST = "-status"`, `FG_SEVERITY_HIGH_FIRST = "-severity"`, `FG_DELTA_NEW_FIRST = "-delta"`, `FG_RECENT_LAST_SEEN = "-last_seen_at"`.

*   Family A preset composite sort strings must have these exact values: `FINDINGS_DEFAULT_SORT = "status,severity,-inserted_at"` (no delta, since plain findings do not expose that field), `FINDINGS_FILTERED_SORT = "severity,-inserted_at"` (status omitted because the API call already applies a status filter), `RESOURCE_DRAWER_OTHER_FINDINGS_SORT = "severity,-updated_at"` (uses `updated_at` because the latest-findings endpoint exposes it).

*   Family B preset composite sort strings must have these exact values: `FINDING_GROUPS_DEFAULT_SORT = "-status,-severity,-delta,-last_seen_at"`, `FINDING_GROUP_RESOURCES_DEFAULT_SORT = "-status,-severity,-delta,-last_seen_at"` (same shape as the groups list), `FINDING_GROUPS_FILTERED_SORT = "-severity,-last_seen_at"` (omits status and delta; must NOT contain `inserted_at`, which is not a valid sort parameter on the grouped API and causes a backend error).

*   The `composeSort` function must accept any number of sort-token strings, join them with commas in the given order (leftmost = highest precedence per JSON:API rules), and return an empty string when called with no arguments.

*   The `findings-filters.ts` module must export two new wire-format string constants: `FAIL_FILTER_VALUE = "FAIL"` and `NEW_DELTA_FILTER_VALUE = "new"`, and must confirm the existing `MUTED_FILTER.EXCLUDE = "false"` and `MUTED_FILTER.INCLUDE = "include"` values.

*   The `applyFailNonMutedFilters` function must accept a `URLSearchParams` instance, mutate it in place (no return value) by setting `filter[status__in]` to `"FAIL"` and `filter[muted]` to `"false"`, overriding any pre-existing values for those keys. All other params must be preserved unchanged.

*   The `splitCsvFilterValues` function must accept `string | string[] | undefined` and return a `string[]`. It must return an empty array for `undefined`, split a string value on commas trimming each token's whitespace, flatten an array input so each element is itself split on commas, and drop all empty tokens produced by stray or trailing commas.

*   The `includesMutedFindings` function must accept `Record<string, string | string[]>` and return `boolean`. It returns `false` when `filter[muted]` is absent from the record, `true` when the value is the string `"include"`, `false` when the value is the string `"false"`, and `true` when the value is an array and any element equals `"include"`.

*   All new symbols from `ui/lib/findings-sort.ts` and `ui/lib/findings-filters.ts` must be re-exported through the `@/lib` barrel (`ui/lib/index.ts`) so that action files can import them via `@/lib` without pulling in server-only code.

*   The findings page source file must call `applyDefaultMutedFilter` with the variable `filtersWithScanDates` as the argument — specifically the exact expression `applyDefaultMutedFilter(filtersWithScanDates)` — so that muted findings are hidden by default unless the caller opts in.

*   The finding-group resource state hook source file must call `applyDefaultMutedFilter` with the variable `filters` as the argument — specifically the exact expression `applyDefaultMutedFilter(filters)` — so that the resource drawer drill-down also hides muted findings by default.


*   Interface details: ## New module: `ui/lib/findings-sort.ts`

This file must be created and export the following symbols.

### Sort token constants

**Family A — plain findings (Postgres ENUM, ascending = FAIL/critical first)**

Type: Constant
Name: FINDINGS_FAIL_FIRST
Location: ui/lib/findings-sort.ts
Value: `"status"` (no minus prefix)

Type: Constant
Name: FINDINGS_SEVERITY_HIGH_FIRST
Location: ui/lib/findings-sort.ts
Value: `"severity"` (no minus prefix)

Type: Constant
Name: FINDINGS_RECENT_INSERT
Location: ui/lib/findings-sort.ts
Value: `"-inserted_at"`

Type: Constant
Name: FINDINGS_RECENT_UPDATE
Location: ui/lib/findings-sort.ts
Value: `"-updated_at"`

**Family B — finding-groups (integer-weighted columns, descending = FAIL/critical/new first)**

Type: Constant
Name: FG_FAIL_FIRST
Location: ui/lib/findings-sort.ts
Value: `"-status"` (must have minus prefix)

Type: Constant
Name: FG_SEVERITY_HIGH_FIRST
Location: ui/lib/findings-sort.ts
Value: `"-severity"` (must have minus prefix)

Type: Constant
Name: FG_DELTA_NEW_FIRST
Location: ui/lib/findings-sort.ts
Value: `"-delta"`

Type: Constant
Name: FG_RECENT_LAST_SEEN
Location: ui/lib/findings-sort.ts
Value: `"-last_seen_at"`

### Preset composite sort strings

**Family A presets (plain findings)**

Type: Constant
Name: FINDINGS_DEFAULT_SORT
Location: ui/lib/findings-sort.ts
Value: `"status,severity,-inserted_at"` — must not contain `delta`, must not minus-prefix `status` or `severity`

Type: Constant
Name: FINDINGS_FILTERED_SORT
Location: ui/lib/findings-sort.ts
Value: `"severity,-inserted_at"` — omits `status` (the API call already applies `filter[status]`)

Type: Constant
Name: RESOURCE_DRAWER_OTHER_FINDINGS_SORT
Location: ui/lib/findings-sort.ts
Value: `"severity,-updated_at"` — uses `updated_at` (not `inserted_at`)

**Family B presets (finding-groups)**

Type: Constant
Name: FINDING_GROUPS_DEFAULT_SORT
Location: ui/lib/findings-sort.ts
Value: `"-status,-severity,-delta,-last_seen_at"` — must minus-prefix status, severity, and delta; status must appear before severity

Type: Constant
Name: FINDING_GROUP_RESOURCES_DEFAULT_SORT
Location: ui/lib/findings-sort.ts
Value: `"-status,-severity,-delta,-last_seen_at"` — same shape as FINDING_GROUPS_DEFAULT_SORT

Type: Constant
Name: FINDING_GROUPS_FILTERED_SORT
Location: ui/lib/findings-sort.ts
Value: `"-severity,-last_seen_at"` — omits status and delta; must NOT contain `inserted_at` (the grouped API does not expose that field)

### Composition helper

Type: Function
Name: composeSort
Location: ui/lib/findings-sort.ts
Signature: composeSort(...tokens: string[]): string
Description: Joins sort token strings with commas in the order given. Returns an empty string when called with no arguments. The leftmost token has highest sort precedence (JSON:API rule).

---

## Extended module: `ui/lib/findings-filters.ts`

The following symbols must be added to (or confirmed present in) this existing file.

### Filter value constants

Type: Constant
Name: FAIL_FILTER_VALUE
Location: ui/lib/findings-filters.ts
Value: `"FAIL"` (exact wire-format string the API expects)

Type: Constant
Name: NEW_DELTA_FILTER_VALUE
Location: ui/lib/findings-filters.ts
Value: `"new"` (exact wire-format string)

Type: Constant
Name: MUTED_FILTER
Location: ui/lib/findings-filters.ts
Description: Object with two properties: `EXCLUDE` equal to `"false"` and `INCLUDE` equal to `"include"`.

### Filter helper functions

Type: Function
Name: applyFailNonMutedFilters
Location: ui/lib/findings-filters.ts
Signature: applyFailNonMutedFilters(params: URLSearchParams): void
Description: Mutates the supplied URLSearchParams in place. Sets `filter[status__in]` to `"FAIL"` and `filter[muted]` to `"false"`, overwriting any pre-existing values for those keys. All other params are left untouched. Returns nothing.

Type: Function
Name: splitCsvFilterValues
Location: ui/lib/findings-filters.ts
Signature: splitCsvFilterValues(value: string | string[] | undefined): string[]
Description: Normalises a filter value into a flat array of non-empty trimmed tokens. Returns `[]` for `undefined`. Splits a single string on commas and trims each piece. Flattens arrays by splitting every element on commas. Drops tokens that are empty after trimming (stray commas produce no entry).

Type: Function
Name: includesMutedFindings
Location: ui/lib/findings-filters.ts
Signature: includesMutedFindings(filters: Record<string, string | string[]>): boolean
Description: Returns `true` when the caller has opted in to including muted findings. Specifically, returns `false` when `filter[muted]` is absent from `filters`; returns `true` when the value is the string `"include"`; returns `false` when the value is the string `"false"`; returns `true` when the value is an array and any element equals `"include"`.

### Existing function (confirmed behaviour)

Type: Function
Name: applyDefaultMutedFilter
Location: ui/lib/findings-filters.ts
Signature: applyDefaultMutedFilter(filters: Record<string, string>): Record<string, string>
Description: Returns a new object that is a copy of `filters` with `filter[muted]` set to `"false"` when the key is absent. If `filter[muted]` is already present in `filters` (e.g. `"include"`), the existing value is preserved unchanged.

---

## Barrel re-exports: `ui/lib/index.ts`

The `@/lib` barrel must re-export all symbols listed above from their respective submodules (`ui/lib/findings-sort.ts` and `ui/lib/findings-filters.ts`) so that action and component code can continue to import them via `@/lib`.

---

## Source-string requirements for page and hook

The file `ui/app/(prowler)/findings/page.tsx` (or `.ts`) must contain the exact call expression:
  `applyDefaultMutedFilter(filtersWithScanDates)`

The file `ui/hooks/use-finding-group-resource-state.ts` must contain the exact call expression:
  `applyDefaultMutedFilter(filters)`


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.