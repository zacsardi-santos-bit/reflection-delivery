Implement support for KNX scenes in the XKNX library. Create a new device class to represent and activate KNX scenes, allowing users to configure and trigger predefined environment settings programmatically. Ensure the library can encode and decode KNX scene numbers and integrate scene configuration into the YAML file format.

*   Implement the `DPTSceneNumber` class in `xknx/knx/dpt_scene_number.py`:
    *   Define class attributes `value_min=0` and `value_max=63`.
    *   Implement `to_knx(value: int) -> tuple`:
        *   Accept integers in the range [0, 63] and return a single-element tuple with the byte value.
        *   Raise `ConversionError` for non-integer input or values outside the valid range.
    *   Implement `from_knx(raw: tuple) -> int`:
        *   Accept a single-element byte tuple in range [0, 63] and return the integer value.
        *   Raise `ConversionError` for tuples with incorrect length, non-integer elements, or byte values exceeding `value_max`.

*   Implement the `Scene` class in `xknx/devices/scene.py`:
    *   Constructor parameters: `xknx`, `name`, `group_address=None`, `scene_number=0`, `device_updated_cb=None`.
    *   Implement `sync(force_refresh: bool) -> coroutine` as a no-op.
    *   Implement `run() -> coroutine` to send a telegram to the group address with a `DPTArray` payload encoding the `scene_number`.
    *   Implement `do(action: str) -> coroutine`:
        *   Call `run()` if `action` is "run".
        *   Log a warning for unrecognized actions with the format 'Could not understand action %s for device %s'.
    *   Implement `has_group_address(group_address: GroupAddress) -> bool` to check if the given address matches the scene's group address.
    *   Implement `__str__()` to return a formatted string: `<Scene name="{name}" scene_value="{group_addr_str}" scene_number="{scene_number}" />`.
    *   Ensure `__eq__(self, other) -> bool` compares objects based on `__dict__`.

*   Update configuration handling:
    *   Extend `xknx/core/config.py` to support a 'scene' section in the YAML configuration file.
    *   Ensure each entry specifies `group_address` and `scene_number`.
    *   Use `Scene.from_config()` to create `Scene` objects from the configuration and add them to `xknx.devices`.

*   Ensure `DPTSceneNumber` is importable from `xknx.knx` and `Scene` from `xknx.devices`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.