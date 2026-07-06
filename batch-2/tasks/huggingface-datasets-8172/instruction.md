I'm working with a dataset where each row has a "document_id" or "category" column that groups related rows together.

*   The `batch` method on both `Dataset` and `IterableDataset` must accept an optional `by_column` parameter that takes either a single column name (string) or a list of column names.

*   When `by_column` is specified, the `batch` method must group consecutive rows with matching values in the specified column(s) into a single batch (run-length encoding). If the same value reappears after a different value, a new batch is started rather than merging with the earlier run.

*   When `by_column` is a list of column names, rows are grouped into a batch only when ALL listed columns have the same values simultaneously across consecutive rows; a batch boundary is created when any of the specified columns changes value.

*   When `batch_size` is specified alongside `by_column`, it must only be used for internal buffering and must NOT limit the output batch size. Each output batch must contain all consecutive rows sharing the same column value(s), regardless of how large that run is.

*   The `batch` method on `IterableDataset` with `by_column` must produce a dataset whose internal iterable supports resumable iteration via state dict and load state dict operations (accessible through `_prepare_ex_iterable_for_iteration`).

*   The `batch_size` parameter on both `Dataset.batch()` and `IterableDataset.batch()` must be optional (defaulting to `None`). At least one of `batch_size` or `by_column` must be provided.


*   Interface details: Type: Method
Name: batch
Location: src/datasets/arrow_dataset.py
Signature: batch(self, batch_size: Optional[int] = None, by_column: Optional[Union[str, list[str]]] = None, drop_last_batch: bool = False, num_proc: Optional[int] = None, new_fingerprint: Optional[str] = None) -> Dataset
Description: Groups dataset samples into batches. When `by_column` is provided (as a string or list of strings), consecutive rows with matching values in the specified column(s) are collected into a single batch (run-length grouping). When `batch_size` is provided alongside `by_column`, it controls internal buffering only, not the output batch size. Both `batch_size` and `by_column` are optional (previously `batch_size` was required).

Type: Method
Name: batch
Location: src/datasets/iterable_dataset.py
Signature: batch(self, batch_size: Optional[int] = None, by_column: Optional[Union[str, list[str]]] = None, drop_last_batch: bool = False) -> IterableDataset
Description: Groups iterable dataset samples into batches. When `by_column` is provided (as a string or list of strings), consecutive rows with matching values in the specified column(s) are collected into a single batch. When `batch_size` is provided alongside `by_column`, it controls internal buffering only. The resulting dataset must support state dict / load state dict for resumable iteration (via _prepare_ex_iterable_for_iteration).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.