## Description

The covariates extraction workflow in graphrag is overly complex, consisting of 6 separate subworkflows that perform incremental transformations (extract_covariates, window, genid, convert, rename, select). This makes the pipeline harder to maintain and understand. Additionally, the Covariate model includes unused fields (`subject_type`, `object_type`, `doc_id`) that are not necessary for the extraction and final output.

The workflow should be simplified by collapsing all the covariate-related steps into a single consolidated verb that handles extraction, ID generation, column renaming, and column selection in one place.

## Expected Behavior

- The `create_final_covariates` workflow should complete in a single step
- The covariate output should include: id, human_readable_id, covariate_type, type, description, subject_id, object_id, status, start_date, end_date, source_text, text_unit_id, document_ids, n_tokens
- The `human_readable_id` column should contain incrementing string values starting at "1"
- The `text_unit_id` column should be renamed from `chunk_id`
- The `subject_type` and `object_type` fields should be removed from the Covariate model
- The `doc_id` field should be removed as it's redundant (chunking happens before extraction)

## Current Behavior

Currently, the covariates workflow consists of 6 subworkflows:
1. `extract_covariates` - Extracts claims from text
2. `window` - Generates UUIDs for id column
3. `genid` - Generates incrementing human_readable_id
4. `convert` - Converts human_readable_id to string
5. `rename` - Renames chunk_id to text_unit_id
6. `select` - Selects final output columns

This multi-step approach is unnecessarily complex and includes unused fields in the Covariate model.
