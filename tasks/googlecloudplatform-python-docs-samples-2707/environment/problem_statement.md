## Add Bigtable Filter and Read Code Snippets

### Description

We need to add Python code snippets demonstrating how to use Google Cloud Bigtable's filtering and reading capabilities. These snippets will serve as documentation examples for developers learning to work with Bigtable.

### Filter Snippets Required

Create `bigtable/snippets/filters/filter_snippets.py` with these filter functions:

**Limiting Filters:**
- `filter_limit_row_sample` - Row sampling with 0.75 probability
- `filter_limit_row_regex` - Filter rows using regex `.*#20190501$` (matches rows ending with #20190501)
- `filter_limit_cells_per_col` - Limit to 2 cells per column
- `filter_limit_cells_per_row` - Limit to 2 cells per row
- `filter_limit_cells_per_row_offset` - Skip first 2 cells in each row
- `filter_limit_col_family_regex` - Filter by regex `stats_.*$` for column family
- `filter_limit_col_qualifier_regex` - Filter by regex `connected_.*$` for column qualifier
- `filter_limit_col_range` - Filter columns in family "cell_plan" from "data_plan_01gb" to "data_plan_10gb" (exclusive end)
- `filter_limit_value_range` - Filter values from "PQ2A.190405" to "PQ2A.190406"
- `filter_limit_value_regex` - Filter values matching `PQ2A.*$`
- `filter_limit_timestamp_range` - Filter with end timestamp datetime(2019, 5, 1)
- `filter_limit_block_all` - Block all cells (returns nothing)
- `filter_limit_pass_all` - Pass all cells through

**Modifying Filters:**
- `filter_modify_strip_value` - Strip values from cells
- `filter_modify_apply_label` - Apply label "labelled" to all cells

**Composing Filters:**
- `filter_composing_chain` - Chain CellsColumnLimitFilter(1) AND FamilyNameRegexFilter("cell_plan")
- `filter_composing_interleave` - Interleave ValueRegexFilter("true") OR ColumnQualifierRegexFilter("os_build")
- `filter_composing_condition` - Conditional filter checking for "data_plan_10gb" with value "true", applying label "passed-filter" if true, "filtered-out" if false

### Read Snippets Required

Create `bigtable/snippets/reads/read_snippets.py` with these read functions:

- `read_row` - Read single row "phone#4c410523#20190501"
- `read_row_partial` - Read same row with ColumnQualifierRegexFilter for "os_build" column only
- `read_rows` - Read specific rows: "phone#4c410523#20190501" and "phone#4c410523#20190502"
- `read_row_range` - Read range from "phone#4c410523#20190501" to "phone#4c410523#201906201"
- `read_row_ranges` - Read two ranges: phone#4c410523 range and phone#5c10102 range
- `read_prefix` - Read all rows with prefix "phone#"
- `read_filter` - Read with ValueRegexFilter pattern "PQ2A.*$"

### Output Format

All snippets must print output in this exact format:

```
Reading data for <row_key>:
Column Family <family_name>
	<qualifier>: <value> @<timestamp>+00:00
	<qualifier>: <value> @<timestamp>+00:00 [label]
```

- First line: `Reading data for <row_key>:`
- Column family header: `Column Family <name>`
- Each cell: tab character, then `<qualifier>: <value> @<timestamp>`
- Labels (if any) appended as ` [<label>]`
- Empty line after each row

### Why This Matters

These code snippets are essential for our documentation. Developers frequently need reference implementations for common Bigtable operations, especially around filtering which can be complex. Having tested, working examples makes it much easier to understand and correctly implement these patterns.
