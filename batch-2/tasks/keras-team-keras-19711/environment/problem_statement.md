I've been poking at Keras's dtype policy system in `@keras/src/dtype_policies` and hitting a bunch of API friction I want cleaned up.

First thing, non-quantized dtype policies (the `DTypePolicy` / `FloatDTypePolicy` path) should let me construct them without passing an explicit dtype name. Right now you always have to give a name even when there's a global policy configured via `dtype_policies.dtype_policy()`. I want it to fall back to the currently configured global dtype policy when no explicit type is given.

Second, there's no clean way to ask whether a policy is quantized. I want a simple boolean property (something like `is_quantized`) on every policy type, returning False for the plain float policies and True for the quantized ones.

Third, the quantized policy API is awkward. Right now you cram the quantization mode and source precision into one specially formatted combined string like `"int8_from_float32"`. I'd rather pass them as separate named params, the quantization mode and the source dtype independently, so the intent's obvious. Oh and passing no source dtype should mean "inherit from the global setting" too, same as the non-quantized case.

Fourth, when a quantized policy serializes to its config dict via `get_config`, it currently just stores the combined name string. I want the config to keep the mode and the source as separate fields so the structure survives round-tripping through `from_config`.

Finally, layers (see `@keras/src/layers/layer.py`) only stash the policy name string in their serialized config. That's not enough when the policy carries extra params beyond a name, so they should serialize the full policy config instead. All of this makes the API explicit and consistent across policy types and lets layers and policies inherit precision from the global setting, which is exactly what you want for mixed-precision training.
