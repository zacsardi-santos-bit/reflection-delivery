Expose the batch coalescing functionality in the arrow-select library to make it accessible from external code. Implement the following requirements to allow users to create and manage coalescers for processing record batches efficiently.

*   Update the arrow-select crate:
    *   Declare the `coalesce` module as public in `arrow-select/src/lib.rs` using `pub mod coalesce`.
    *   Ensure `BatchCoalescer` is declared as `pub` in `arrow-select/src/coalesce.rs`.

*   Implement the `BatchCoalescer` struct:
    *   `new(schema: SchemaRef, batch_size: usize) -> Self`
        *   Accept a schema reference (`Arc<Schema>` / `SchemaRef`) and a target batch size (`usize`).
        *   Return a new instance of `BatchCoalescer`.

    *   `push_batch(&mut self, batch: RecordBatch) -> Result<(), ArrowError>`
        *   Accept a `RecordBatch` by value.
        *   Buffer the batch internally.
        *   Return `Ok(())` on success or an `ArrowError` on failure.

    *   `finish_buffered_batch(&mut self) -> Result<(), ArrowError>`
        *   Concatenate all buffered batches into a single completed batch.
        *   Clear the internal buffer.
        *   Return `Ok(())` on success or an `ArrowError` on failure.

    *   `next_completed_batch(&mut self) -> Option<RecordBatch>`
        *   Return `Some(batch)` for each completed batch in order.
        *   Return `None` when no completed batches remain.

*   Ensure functionality:
    *   When a batch with fewer rows than the target size is pushed and `finish_buffered_batch` is called, `next_completed_batch` must return a `RecordBatch` with the correct number of rows.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.