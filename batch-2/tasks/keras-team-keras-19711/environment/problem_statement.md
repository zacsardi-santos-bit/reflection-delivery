## Description

The current API for creating quantized dtype policies requires passing a specially formatted combined string as a single argument. This is opaque: it bundles the quantization mode and the source precision into one string, making it hard to understand and error-prone to construct. There is also no way to create a quantized policy that automatically inherits from the currently active global dtype setting.

Additionally, the existing policy classes lack a built-in indicator to easily tell whether a given policy is quantized or not. The serialization format for quantized policies only stores the combined name string, which loses the structured information.

Finally, layers currently store only the policy name string in their serialized configuration, which is insufficient when the policy carries additional parameters beyond just a name.

## Expected Behavior

- Non-quantized dtype policies should support being initialized without an explicit dtype, falling back to the currently configured global dtype policy.
- Non-quantized policies should expose a property indicating they are not quantized.
- Quantized dtype policies should be constructable by specifying the quantization mode and source dtype as separate, explicit parameters rather than a fused name string.
- Quantized policies should also support inheriting the source dtype from the global policy when none is specified.
- Quantized policies should expose a property indicating they are quantized.
- The configuration produced by quantized policies should store the mode and source as separate fields, not as a combined name string.
- Layers should serialize their dtype policy using the full policy configuration, not just the policy name.

## Why This Matters

This makes the API explicit, structured, and consistent across policy types. It enables workflows where layers and policies inherit their precision from the global setting, which is a common use pattern for mixed-precision training.
