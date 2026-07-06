I'm working with the HuggingFace datasets library and I need to add support for loading data from Apache Iceberg tables.

*   When loading data via the 'iceberg' module with a valid catalog object and a string table identifier, the result must be a DatasetDict containing a 'train' split with the correct number of rows, all expected column names, and correct data values.

*   When the 'columns' parameter is provided as a list of column names, only those columns must appear in the resulting dataset's column names; columns not in the list must be absent.

*   When the 'filters' parameter is provided as a SQL-style string predicate (e.g. 'col > value'), only rows satisfying the predicate must be returned.

*   When the 'table' parameter is provided as a dict mapping split names to table identifiers (e.g. {'train': 'db.t1', 'test': 'db.t2'}), the result must be a DatasetDict with each split name as a key and the correct row count per split.

*   When 'split' and 'streaming=True' are provided, the result must be an IterableDataset (instance of datasets.IterableDataset) that yields all rows with correct fields. When 'streaming=False' (or omitted), the result must be a regular Dataset.

*   When the 'snapshot_id' parameter is provided as an integer Iceberg snapshot identifier, only the rows present at that snapshot must be returned; the latest (no snapshot_id) must return all rows including those added after the snapshot.

*   When 'num_proc' is set to a value greater than 1, the loader must still return the complete and correct dataset (all rows, correct values).

*   When 'catalog=None' is passed, a ValueError must be raised and the error message must contain the string 'catalog'.

*   When 'table=None' is passed, a ValueError must be raised and the error message must contain the string 'table'.

*   A 'require_pyiceberg' decorator must be added to tests/utils.py that skips the decorated test when the PyIceberg library is not installed.


*   Interface details: Type: Function
Name: require_pyiceberg
Location: tests/utils.py
Signature: require_pyiceberg(test_case) -> test_case
Description: Decorator that skips the decorated test if the PyIceberg library is not installed. Follows the same pattern as existing decorators such as require_sqlalchemy. When PyIceberg is importable, returns the test case unchanged; otherwise marks it as skipped with the message "test requires pyiceberg".

Type: Module
Name: iceberg
Location: src/datasets/packaged_modules/iceberg/iceberg.py
Description: New packaged module implementing Apache Iceberg table loading. Must be registered in src/datasets/packaged_modules/__init__.py under the key "iceberg" in _PACKAGED_DATASETS_MODULES. The module must be importable as `from .iceberg import iceberg` in that __init__.py.

Type: Class
Name: IcebergConfig
Location: src/datasets/packaged_modules/iceberg/iceberg.py
Description: BuilderConfig subclass for the Iceberg module. Holds configuration fields and validates required parameters on initialization.
Signature:
  Fields:
    catalog: Optional[Catalog] = None          # PyIceberg Catalog object (required; raises ValueError mentioning "catalog" if None)
    table: Optional[Union[str, Dict[str, str]]] = None  # Table identifier string or split->table dict (required; raises ValueError mentioning "table" if None)
    features: Optional[Features] = None        # Optional feature schema override
    columns: Optional[List[str]] = None        # Optional list of column names to project
    filters: Optional[Union[str, BooleanExpression]] = None  # Optional SQL-style row filter string or pyiceberg BooleanExpression
    batch_size: int = 131072                   # Rows per RecordBatch
    snapshot_id: Optional[int] = None         # Optional Iceberg snapshot ID for time-travel

Type: Class
Name: Iceberg
Location: src/datasets/packaged_modules/iceberg/iceberg.py
Description: ArrowBasedBuilder subclass that implements the Iceberg dataset loader. Uses IcebergConfig as its BUILDER_CONFIG_CLASS. Must support loading data from an Iceberg catalog, column projection, row filtering, multiple splits, snapshot time-travel, and parallel processing via num_proc.
Signature:
  BUILDER_CONFIG_CLASS = IcebergConfig
  Methods:
    _info(self) -> DatasetInfo
    _split_generators(self, dl_manager) -> List[SplitGenerator]
    _generate_tables(self, tasks, scan_context) -> Iterator[Tuple[Key, pa.Table]]


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.