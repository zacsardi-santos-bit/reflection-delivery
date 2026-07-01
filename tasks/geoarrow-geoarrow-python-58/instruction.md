Implement support for a new CRS type that wraps a plain string, allowing users to specify CRS using authority strings instead of full PROJJSON objects. Update serialization to include a discriminator field for structured CRS objects to ensure reliable round-tripping of type metadata.

*   Create a new `StringCrs` class in `geoarrow.types.crs`:
    *   Constructor `__init__(self, crs: Union[str, bytes]) -> None`:
        *   Accepts `str` or `bytes`; decodes `bytes` to `str` and stores in `_crs`.
    *   Method `__geoarrow_crs_json_values__(self) -> dict`:
        *   Returns `{"crs": value}` where `value` is the unwrapped CRS.
    *   Method `__repr__(self) -> str`:
        *   Returns `"StringCrs({unwrapped})"` using the unwrapped CRS.
    *   Method `to_json(self) -> str`:
        *   Returns JSON object string if valid; otherwise delegates to `pyproj`.
    *   Method `to_json_dict(self) -> dict`:
        *   Returns `json.loads(self.to_json())`.
    *   Method `to_wkt(self) -> str`:
        *   Delegates to `pyproj` using the unwrapped CRS.

*   Update `ProjJsonCrs` class in `geoarrow.types.crs`:
    *   Add method `to_wkt(self) -> str`:
        *   Returns `pyproj.CRS(self.to_json_dict()).to_wkt()`.

*   Modify `TypeSpec` class behavior:
    *   Method `extension_metadata()`:
        *   Serialize `StringCrs` using `__geoarrow_crs_json_values__()`.
        *   Serialize raw `str` or `bytes` as `StringCrs`.
        *   Serialize CRS with `to_json_dict()` as `projjson`.
        *   Raise `ValueError` if CRS is unspecified.
    *   Method `from_extension_metadata()`:
        *   Deserialize `{"crs": "string-value"}` as `StringCrs`.
        *   Deserialize `{"crs": {...}, "crs_type": "projjson"}` as `ProjJsonCrs`.
    *   Method `override(crs='string')`:
        *   Normalize raw string CRS to `StringCrs`.
        *   Preserve existing CRS objects as-is.

*   Ensure `with_crs()` function and method:
    *   Accept plain string CRS and store as `StringCrs`.

*   Update `GeoArrowExtensionDtype` string representation:
    *   Include `"crs_type": "projjson"` after `"crs": ...` for `ProjJsonCrs`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.