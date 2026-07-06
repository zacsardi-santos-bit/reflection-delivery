Implement a publish-subscribe utility class in Modin to allow dynamic reconfiguration of the execution engine and partition format settings at runtime. Ensure that changes to these settings notify registered listeners and update the engine dispatcher automatically.

*   Implement the `Publisher` class in `modin/__init__.py`:
    *   Constructor `__init__(self, name: str, value: Any) -> None` must initialize with a name and an initial value.
    *   Method `get(self) -> Any` must return the current value.
    *   Method `put(self, value: Any) -> None` must update the stored value and notify all subscribers if the value changes.
    *   Method `subscribe(self, callback: Callable[[Publisher], None]) -> None` must register a callback that is invoked immediately and on every future value change.
    *   Method `once(self, onvalue: Any, callback: Callable[[Publisher], None]) -> None` must invoke the callback immediately if the current value equals `onvalue`, otherwise store it to be invoked once when the value transitions to `onvalue`.

*   Ensure `Publisher` is importable from the modin top-level package and accessible in the `modin.pandas` module namespace.
*   Create `execution_engine` and `partition_format` as `Publisher` instances in `modin/__init__.py`, importable directly from modin.
*   Implement `EngineDispatcher` in `modin/data_management/dispatcher.py`:
    *   Class method `get_engine(cls) -> type[factories.BaseFactory]` must return the currently active factory class.
    *   Class method `_update_engine(cls, publisher: Publisher) -> None` must update the active engine factory based on current values of `execution_engine` and `partition_format`.
    *   Ensure `EngineDispatcher` automatically updates its active factory when `execution_engine.put(engine_name)` or `partition_format.put(format_name)` is called.
    *   Raise `FactoryNotFoundError` if no corresponding factory class exists for the given engine or format in non-experimental mode.

*   Ensure `FactoryNotFoundError` is importable from `modin.data_management.dispatcher`.
*   Rename the Dask distributed client variable in `modin/pandas/__init__.py` to `dask_client`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.