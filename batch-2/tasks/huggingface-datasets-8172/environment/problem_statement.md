## Description

When working with datasets that have natural groupings — for example, multiple rows belonging to the same document, the same speaker, or the same experimental run — there is currently no way to collect all rows from the same group into a single batch. The only available batching option uses a fixed number of rows per batch, which can split a group across multiple batches and makes it hard to process related rows together.

## Expected Behavior

The batching API should support a "group by column" mode where consecutive rows sharing the same value in one or more specified columns are collected into a single batch. For example:

- If a dataset has a category column where the values follow the pattern A, A, B, B, C, B, grouping by that column should produce four batches: one for the first run of A, one for B, one for C, and another for the second run of B.
- Grouping should be based on **consecutive runs**, not global sorting. If the same value appears in two separate stretches, they produce two separate batches.
- It should also be possible to group by multiple columns simultaneously, so that a batch boundary is created whenever any of the specified columns changes value.
- When a fixed batch size is also provided, it should serve as a processing buffer hint only — it must not limit how many rows end up in a single output batch.

## Why This Matters

This feature enables natural processing of grouped data without requiring the user to pre-sort or pre-group records beforehand. It is especially useful for NLP workflows where multiple rows represent sentences or tokens from the same document, or time-series data where multiple measurements belong to the same event window.
