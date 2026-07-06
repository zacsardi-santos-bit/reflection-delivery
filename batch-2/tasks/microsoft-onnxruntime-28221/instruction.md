I'm working on making the static quantization pipeline more efficient by adding a calibration cache.

*   TensorData must be constructible with positional arguments lowest and highest (numpy scalar float32 arrays) and optional keyword arguments hist, hist_edges, and bins. It must expose a range_value property returning the (lowest, highest) tuple, and attributes hist, hist_edges, and bins.

*   TensorsData must be constructible with a CalibrationMethod and a dict mapping string keys to TensorData objects. It must expose a calibration_method attribute, support dict-style key access via __getitem__, and provide a keys() method.

*   save_tensors_data must serialize a TensorsData to a JSON file at the given path, automatically creating any missing parent directories. The resulting JSON must include a top-level 'smooth_quant' boolean field matching the smooth_quant argument (default False). Scalar numpy bin values must be saved as plain integers.

*   load_tensors_data must deserialize a TensorsData from a JSON file, faithfully restoring numpy arrays (including scalar arrays with shape ()) and their dtypes. It must raise FileNotFoundError if the given path does not exist.

*   load_tensors_data must treat a JSON cache file that lacks a 'smooth_quant' key as having smooth_quant=False (backward compatibility with legacy cache files).

*   quantize_static must accept a calibration_cache_path keyword argument. When calibration_data_reader is provided and calibration_cache_path is specified, quantize_static must save the calibration results to that path after calibration completes.

*   When calibration_data_reader is None and calibration_cache_path points to an existing file whose calibration method matches the current calibrate_method, quantize_static must load calibration from the cache and proceed with quantization without requiring a data reader.

*   When calibration_data_reader is None and no valid cache is available (file missing), quantize_static must raise ValueError.

*   When calibration_data_reader is None and the cache file's calibration method does not match the requested calibrate_method, quantize_static must raise ValueError.

*   When quantize_static loads a cache whose smooth_quant field does not match the current smooth_quant setting, it must emit at least one WARNING-level log message containing the word 'smooth_quant', treat the cache as a miss, recompute calibration using the provided data reader, and overwrite the cache with smooth_quant set to the current value.


*   Interface details: Type: Class
Name: TensorData
Location: onnxruntime/python/tools/quantization/calibrate.py
Description: Holds calibration statistics for a single tensor. Stores minimum/maximum values and optional histogram data.
Signature: TensorData(lowest: np.ndarray, highest: np.ndarray, hist: np.ndarray = None, hist_edges: np.ndarray = None, bins = None)
Properties:
  - range_value: property returning (lowest, highest) as a tuple
  - hist: numpy array of histogram bin counts
  - hist_edges: numpy array of histogram bin edges
  - bins: scalar integer bin count (scalar numpy integers are stored/returned as plain int)

Type: Class
Name: TensorsData
Location: onnxruntime/python/tools/quantization/calibrate.py
Description: Container mapping tensor names to TensorData objects along with the calibration method used.
Signature: TensorsData(calibration_method: CalibrationMethod, data: dict)
Properties/Methods:
  - calibration_method: CalibrationMethod attribute
  - __getitem__(key: str) -> TensorData: dict-style access by tensor name
  - keys() -> collection of tensor name strings

Type: Function
Name: save_tensors_data
Location: onnxruntime/python/tools/quantization/calibrate.py
Signature: save_tensors_data(tensors_data: TensorsData, path: Path, smooth_quant: bool = False) -> None
Description: Serializes a TensorsData object to a JSON file at the given path. Automatically creates any missing parent directories. The output JSON must include a top-level "smooth_quant" boolean field equal to the smooth_quant argument. Scalar numpy values (e.g., np.int64 bins) must be serialized as plain integers.

Type: Function
Name: load_tensors_data
Location: onnxruntime/python/tools/quantization/calibrate.py
Signature: load_tensors_data(path: Path) -> TensorsData
Description: Deserializes a TensorsData object from a JSON file. Raises FileNotFoundError if the file does not exist. Reconstructs numpy arrays with their original dtypes and shapes (scalar arrays must have shape ()). If the JSON file does not contain a "smooth_quant" field (legacy cache), it is treated as smooth_quant=False.

Type: Function
Name: quantize_static
Location: onnxruntime/python/tools/quantization/quantize.py
Signature: quantize_static(..., calibration_cache_path: Path = None, ...) -> None
Description: Extends the existing quantize_static function with a calibration_cache_path keyword parameter. Behavior:
  - If calibration_data_reader is provided and calibration_cache_path is given: run calibration normally, then save the resulting TensorsData to calibration_cache_path (creating parent dirs as needed).
  - If calibration_data_reader is None and calibration_cache_path points to an existing file with matching calibration_method and smooth_quant: load calibration from cache and proceed without recalibrating.
  - If calibration_data_reader is None and no valid cache is available (file missing, method mismatch, or smooth_quant mismatch with no reader to recompute): raise ValueError.
  - If the loaded cache's calibration_method does not match the current calibrate_method parameter: raise ValueError.
  - If the loaded cache's smooth_quant field does not match the current smooth_quant setting and a calibration_data_reader is available: emit a WARNING-level log message containing the string "smooth_quant", treat the cache as a miss, recompute calibration, and overwrite the cache with smooth_quant set to the current value.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.