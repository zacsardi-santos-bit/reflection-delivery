Implement Python code snippets demonstrating Google Cloud Bigtable's filtering and reading capabilities. Create separate modules for filter and read operations, each containing specific functions as described.

*   Create a filter_snippets module at `bigtable/snippets/filters/filter_snippets.py`:
    *   Implement 18 filter functions, each accepting `project_id`, `instance_id`, and `table_id` parameters.
    *   Ensure each function applies the specified filter type and prints output in the required format.
    *   Use the following filter functions:
        *   `filter_limit_row_sample`: Apply `RowSampleFilter(0.75)`.
        *   `filter_limit_row_regex`: Use regex `.*#20190501$` to filter rows.
        *   `filter_limit_cells_per_col`: Use `CellsColumnLimitFilter(2)`.
        *   `filter_limit_cells_per_row`: Use `CellsRowLimitFilter(2)`.
        *   `filter_limit_cells_per_row_offset`: Use `CellsRowOffsetFilter(2)`.
        *   `filter_limit_col_family_regex`: Use regex `stats_.*$`.
        *   `filter_limit_col_qualifier_regex`: Use regex `connected_.*$`.
        *   `filter_limit_col_range`: Filter columns in family "cell_plan" from "data_plan_01gb" to "data_plan_10gb".
        *   `filter_limit_value_range`: Filter values from "PQ2A.190405" to "PQ2A.190406".
        *   `filter_limit_value_regex`: Use regex `PQ2A.*$`.
        *   `filter_limit_timestamp_range`: Use end timestamp `datetime(2019, 5, 1)`.
        *   `filter_limit_block_all`: Demonstrate blocking all cells.
        *   `filter_limit_pass_all`: Demonstrate passing all cells.
        *   `filter_modify_strip_value`: Strip cell values.
        *   `filter_modify_apply_label`: Apply label "labelled".
        *   `filter_composing_chain`: Chain `CellsColumnLimitFilter(1)` with `FamilyNameRegexFilter("cell_plan")`.
        *   `filter_composing_interleave`: Interleave `ValueRegexFilter("true")` with `ColumnQualifierRegexFilter("os_build")`.
        *   `filter_composing_condition`: Conditional filter with labels "passed-filter" and "filtered-out".
    *   Implement a `print_row(row)` helper function to format output as specified.

*   Create a read_snippets module at `bigtable/snippets/reads/read_snippets.py`:
    *   Implement 7 read functions, each accepting `project_id`, `instance_id`, and `table_id` parameters.
    *   Ensure each function reads data as specified and prints output in the required format.
    *   Use the following read functions:
        *   `read_row`: Read row "phone#4c410523#20190501".
        *   `read_row_partial`: Read row with `ColumnQualifierRegexFilter` for "os_build".
        *   `read_rows`: Read rows "phone#4c410523#20190501" and "phone#4c410523#20190502" using `RowSet`.
        *   `read_row_range`: Read range from "phone#4c410523#20190501" to "phone#4c410523#201906201".
        *   `read_row_ranges`: Read two ranges: phone#4c410523 and phone#5c10102.
        *   `read_prefix`: Read all rows with prefix "phone#".
        *   `read_filter`: Use `ValueRegexFilter` with pattern `PQ2A.*$`.
    *   Implement a `print_row(row)` helper function to format output as specified.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.