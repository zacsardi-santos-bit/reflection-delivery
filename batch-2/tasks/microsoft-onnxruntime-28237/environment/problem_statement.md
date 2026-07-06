## Description

When performing static quantization of neural networks to unsigned 8-bit integers, certain deployment targets require activation zero-points to be restricted to specific canonical values rather than computed freely from calibration statistics. For all-positive activations (such as those that follow a rectification operation), the zero-point should be fixed at the minimum of the quantized range so that the value zero is exactly representable. For activations that span both positive and negative values, the zero-point should be fixed at the midpoint of the quantized range to provide balanced coverage.

Currently there is no way to request this constrained zero-point behavior without switching to symmetric quantization, which loses precision for one-sided distributions.

## Expected Behavior

- A new quantization option should allow users to enable restricted asymmetric zero-point snapping for activations when using unsigned 8-bit quantization.
- When the option is enabled and the calibrated minimum activation value is non-negative, the activation zero-point should be set to the minimum of the quantized range (zero), and the scale recomputed to cover the calibrated range without clipping.
- When the option is enabled and the calibrated minimum activation value is negative, the activation zero-point should be set to the midpoint of the quantized range (128 for standard unsigned 8-bit), and the scale recomputed to cover both halves.
- When the option is disabled, existing standard asymmetric quantization behavior must be preserved.
- A utility function implementing the snapping logic should be publicly accessible so developers can apply it directly outside of the full quantization pipeline.
- The utility function must respect a narrowed quantized range mode and must also enforce a minimum scale floor when a minimum real range is specified.
- Degenerate calibration data (all values zero) should be handled gracefully, snapping to the minimum of the quantized range.

## Why This Matters

Hardware and inference runtimes that operate most efficiently with constrained zero-points cannot be targeted easily with free asymmetric quantization. This change lets users produce quantized models that satisfy these constraints without sacrificing the precision benefits of asymmetric quantization for one-sided activation distributions.
