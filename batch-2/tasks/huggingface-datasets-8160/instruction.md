I work with IoT and sensor datasets stored in a time-series file format, and I'd like to load them directly through the standard datasets loading infrastructure without writing custom parsing code.

*   TsFileConfig must raise InvalidConfigName with a message matching 'Bad characters' when constructed with a name containing invalid characters; it must raise ValueError with a message matching 'Expected a DataFilesDict' when data_files is a plain string, a plain list, or a DataFilesList instead of a DataFilesDict.

*   TsFileConfig must raise ValueError with a message mentioning the relevant parameter for: input_batch_size <= 0 (message matches 'input_batch_size'), output_batch_size <= 0 (message matches 'output_batch_size'), columns=[] empty list (message matches 'non-empty'), an unsupported timestamp_unit value (message matches 'timestamp_unit'), and an unsupported on_bad_files value (message matches 'on_bad_files').

*   TsFileConfig must normalize its start_time and end_time attributes to plain Python integers: a pyarrow timestamp scalar passed as start_time or end_time must be stored as its integer epoch value; an integer passed directly must be stored as-is.

*   _to_epoch must return an integer unchanged when given an integer value; treat naive datetime objects as UTC and return their epoch in the given unit; treat timezone-aware datetime objects and ISO-8601 strings with UTC-offset suffixes by converting to UTC before encoding; treat date objects as midnight UTC; treat pyarrow timestamp scalars by returning their underlying integer value.

*   _to_epoch must raise TypeError with a message matching 'bool' when given a boolean value; it must raise TypeError with a message matching 'must be a' for any other unsupported input type (bytes, arbitrary objects, strings that are not valid ISO-8601 date/datetime).

*   The tsfile packaged module, loadable via load_dataset('tsfile', ...), must produce a dataset in wide format: one row per unique device (i.e., unique combination of TAG column values), with TAG columns stored as scalar values and TIME and FIELD columns stored as Python lists aligned in time order.

*   The output column order must be: TAG columns first (in schema order), then the time column, then FIELD columns. All column names must be lowercased.

*   The columns parameter must filter which FIELD columns appear in the output; TAG and TIME columns must always be present regardless of what columns contains. If columns includes a TAG or TIME column name, those must still appear exactly once (the request is silently ignored as a duplicate). If columns names a field absent from the file, that column must appear with a null-filled list of the correct length.

*   start_time and end_time parameters must filter the time-series points included in each row, accepting any input type that _to_epoch accepts: plain integers, datetime objects, date objects, ISO-8601 strings (with or without timezone offsets), and pyarrow timestamp scalars.

*   When loading multiple files for the same table, rows for the same device must be merged across files by concatenating their time and field lists in time-sorted order. Fields absent in some files must be null-filled for those time points (schema evolution support).

*   When the same device has an identical timestamp value in two different source files, loading must raise an exception; the exception or its cause chain must contain a message with 'Duplicate timestamp'.

*   When the same field has different numeric types across files being merged, numeric types must be promoted: INT32 widens to INT64; FLOAT widens to DOUBLE (float64); INT32 combined with DOUBLE must produce DOUBLE (float64).

*   All supported data types must load correctly and map to the appropriate Python types: BOOLEAN to bool, INT32/INT64 to int, FLOAT/DOUBLE to float, TEXT/STRING to str, TIMESTAMP to datetime, DATE to date, BLOB to bytes.

*   The table_name parameter must select which table to load from a multi-table file; when omitted, the first-registered table in the file must be used as the default.

*   The on_bad_files parameter must control behavior for corrupted or unreadable files: the default (equivalent to 'raise') must raise an exception whose chain contains a message with 'not a valid TsFile'; 'skip' must silently omit the bad file and return only data from valid files; 'warn' must also omit the bad file but additionally emit a WARNING-level log message containing 'Skipping bad file' to the logger named 'datasets.packaged_modules.tsfile.tsfile'. The streaming=True parameter must return an IterableDataset. The timestamp_tz parameter must attach the specified timezone to timestamp columns. The input_batch_size parameter must control the Arrow reading batch size.


*   Interface details: Type: Class
Name: TsFileConfig
Location: src/datasets/packaged_modules/tsfile/tsfile.py
Description: Configuration class for the tsfile packaged module. Must extend datasets.BuilderConfig (calling super().__post_init__() to inherit name validation and data_files type checking). Validates all constructor arguments and normalizes time-bound parameters to integer epochs.
Signature:
  __init__(
    name: str,
    data_files: DataFilesDict | None = None,
    input_batch_size: int = ...,    # default > 0
    output_batch_size: int = ...,   # default > 0
    columns: list[str] | None = None,
    timestamp_unit: str = ...,      # valid: "s", "ms", "us", "ns"; default "ms"
    on_bad_files: str = ...,        # valid: "error", "warn", "skip"; default "error"
    start_time: int | datetime | date | str | pa.Scalar | None = None,
    end_time: int | datetime | date | str | pa.Scalar | None = None,
    table_name: str | None = None,
    timestamp_tz: str | None = None,
  ) -> None
  Attributes after __post_init__:
    start_time: int | None   (normalized from any accepted input type to integer epoch)
    end_time: int | None     (normalized from any accepted input type to integer epoch)

Type: Function
Name: _to_epoch
Location: src/datasets/packaged_modules/tsfile/tsfile.py
Signature: _to_epoch(value: int | datetime | date | str | pa.Scalar, unit: str) -> int
Description: Converts a timestamp representation to an integer epoch in the specified unit.
  - int: returned unchanged
  - naive datetime: treated as UTC, converted to epoch
  - aware datetime: converted to UTC first, then to epoch
  - date: treated as midnight UTC, converted to epoch
  - ISO-8601 str (with or without timezone offset): parsed, converted to UTC if needed, then to epoch
  - pa.Scalar of timestamp type: converted to its underlying integer epoch value
  - bool: raises TypeError with a message containing "bool"
  - any other unsupported type (bytes, arbitrary objects, non-date strings): raises TypeError with a message containing "must be a"

Note: The "tsfile" packaged module must be registered in src/datasets/packaged_modules/__init__.py in _PACKAGED_DATASETS_MODULES and _EXTENSION_TO_MODULE (for the ".tsfile" extension), so that load_dataset("tsfile", ...) resolves correctly. This also requires creating src/datasets/packaged_modules/tsfile/__init__.py (may be empty).

Note: The module's logger must be obtained via datasets.utils.logging.get_logger(__name__) where __name__ == "datasets.packaged_modules.tsfile.tsfile", because tests assert that WARNING-level "Skipping bad file" messages appear in that specific logger.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.