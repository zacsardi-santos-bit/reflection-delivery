I'm working on deploying a quantized neural network to a hardware target that requires activation zero-points to be set to specific canonical values.

*   Must add a function snap_zero_point_to_uint8 to onnxruntime/python/tools/quantization/quant_utils.py with signature: snap_zero_point_to_uint8(rmin, rmax, qmin: int = 0, qmax: int = 255, min_real_range: float | None = None).

*   snap_zero_point_to_uint8 must return a tuple (zero_point, scale) where zero_point is a numpy uint8 array and scale is a numpy float32 array.

*   When rmin >= 0, snap_zero_point_to_uint8 must return zero_point equal to qmin (e.g., 0 for standard uint8 with qmin=0).

*   When rmin < 0, snap_zero_point_to_uint8 must return zero_point equal to (qmin + qmax + 1) // 2 (e.g., 128 for standard uint8 with qmin=0, qmax=255).

*   When called with qmin=0 and qmax=127 (reduce_range mode), snap_zero_point_to_uint8 must return zero_point in [0, 127] and scale > 0.

*   When min_real_range is provided, snap_zero_point_to_uint8 must return scale >= min_real_range / (qmax - qmin). For example, with rmin=-1e-9, rmax=1e-9, qmin=0, qmax=255, min_real_range=1e-4, scale must be >= 1e-4 / 255.

*   snap_zero_point_to_uint8 must handle degenerate calibration ranges (rmin == rmax == 0): zero_point must snap to qmin (0 for standard uint8).

*   The static quantization function must support a new extra_options key 'ActivationRestrictedAsymmetric' (bool, default False).

*   When 'ActivationRestrictedAsymmetric' is True and the activation quantization type is uint8 (unsigned 8-bit) and quantization is not symmetric, snap_zero_point_to_uint8 must be used to compute the activation zero_point and scale instead of the standard asymmetric formula.

*   When activations are all non-negative (rmin >= 0) and 'ActivationRestrictedAsymmetric' is True, the activation zero_point in the output quantized model must equal 0.

*   When activations span negative values (rmin < 0) and 'ActivationRestrictedAsymmetric' is True, the activation zero_point in the output quantized model must equal 128 (for standard uint8 [0, 255]).

*   When all calibration data is zero (degenerate all-zero range) and 'ActivationRestrictedAsymmetric' is True, the activation zero_point in the output quantized model must equal 0 (qmin).

*   When 'ActivationRestrictedAsymmetric' is False, standard asymmetric quantization must be used without snapping (e.g., for rmin=-1, rmax=2, the resulting zero_point must NOT equal 128).


*   Interface details: Type: Function
Name: snap_zero_point_to_uint8
Location: onnxruntime/python/tools/quantization/quant_utils.py
Signature: snap_zero_point_to_uint8(rmin, rmax, qmin: int = 0, qmax: int = 255, min_real_range: float | None = None) -> tuple[numpy.ndarray, numpy.ndarray]
Description: Snaps a uint8 activation zero-point to a canonical value based on the sign of the calibrated minimum. When rmin >= 0, zero_point is snapped to qmin (e.g., 0). When rmin < 0, zero_point is snapped to the midpoint (qmin + qmax + 1) // 2 (e.g., 128 for standard uint8 [0, 255]). Recomputes the scale so the dequantized range covers [rmin, rmax] without clipping. Returns (zero_point, scale) where zero_point is a numpy uint8 scalar array and scale is a numpy float32 scalar array. Handles degenerate ranges (rmin == rmax) and the min_real_range floor on scale.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.