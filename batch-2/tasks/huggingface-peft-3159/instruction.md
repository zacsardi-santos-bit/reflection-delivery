I'm working on reducing the peak GPU memory usage during LoRA fine-tuning of large language models.

*   VeloraConfig must be a dataclass with fields num_groups (int, default 64), scale (float, default 1.0), and init_type (str, default 'batch_average'). It must raise ValueError with the exact message "`num_groups` should be positive, got {num_groups}." when num_groups is not positive, with the exact message "`scale` should be positive, got {scale}." when scale is not positive, and with the exact message "Unsupported `init_type` '{init_type}'. Supported values are 'batch_average_once', 'batch_average', and 'random'." when init_type is not one of those three values.

*   VeloraConfig must be importable from peft (top-level), peft.tuners, peft.tuners.lora, and peft.tuners.lora.config. The class accessible at peft.tuners.lora.VeloraConfig must be identical to peft.VeloraConfig (same class object), so models created with either reference produce identical configurations and state dicts.

*   LoraConfig must accept an optional velora_config parameter (default None) that takes a VeloraConfig instance. When provided, the peft_config stored on the resulting peft model must have a velora_config attribute with num_groups, init_type, and scale matching the input VeloraConfig, and peft_type must be PeftType.LORA.

*   The module peft.tuners.lora.velora must exist and export: VeloraFunction, _compress_activations, _normalize_projection, _reconstruct_activations, and _reshape_to_grouped_subtokens.

*   _reshape_to_grouped_subtokens(x, num_groups) must flatten all leading dimensions of x, pad the last dimension with zeros to the next multiple of num_groups, and return a tensor of shape (N, num_groups, ceil(in_features/num_groups)). For example: input shape (2, 5) with num_groups=3 returns shape (2, 3, 2) with zero-padding; input (2, 2, 3) with num_groups=2 returns shape (4, 2, 2) with zero-padding.

*   _compress_activations(x, embed, num_groups) must project the grouped subtokens of x onto embed and return a tensor of shape (N, num_groups).

*   _reconstruct_activations(compressed, embed, in_features, velora_scale) must reconstruct an approximation of the original activations from compressed form and return a tensor of shape (N, in_features) scaled by velora_scale.

*   _normalize_projection(embed) must L2-normalize the input vector and return the result.

*   VeloraFunction must be a torch.autograd.Function whose apply(x, weight, bias, embed, num_groups, velora_scale) forward pass returns F.linear(x, weight, bias) and whose backward pass computes: grad_input via grad_output @ weight (standard), grad_weight via reconstructed activations X_hat (from _reconstruct_activations of compressed input) rather than the original input X, and grad_bias as the sum over the batch dimension.

*   When VeLoRA is applied to a linear layer, the layer must have a lora_velora_embed dict-like attribute keyed by adapter name whose value is a tensor of shape (ceil(in_features/num_groups),), and a lora_velora_initialized dict keyed by adapter name whose value is a bool.

*   For init_type='random', lora_velora_embed must be initialized with a normalized random vector and lora_velora_initialized must be True from the start. For init_type='batch_average_once', the embed must be computed from the first training forward pass (using batch statistics) and then frozen, with lora_velora_initialized becoming True after that first pass and remaining True. For init_type='batch_average', the embed must be recomputed from the batch statistics on every training forward pass.

*   Using VeLoRA must result in fewer bytes of saved tensors during the backward pass compared to vanilla LoRA on the same model and input, confirming that compressed activations (not the full input) are stored between forward and backward.

*   The base layer weight must receive no gradient when VeLoRA is used (grad should be None), while lora_A and lora_B weights receive gradients consistent with the VeLoRA update rule.


*   Interface details: Type: Class
Name: VeloraConfig
Location: src/peft/tuners/lora/config.py
Description: Dataclass holding VeLoRA sub-configuration. Must be exported from peft (top-level), peft.tuners, peft.tuners.lora, and peft.tuners.lora.config. The class at peft.tuners.lora.VeloraConfig must be the same class (alias) as peft.VeloraConfig.
Signature:
  __init__(num_groups: int = 64, scale: float = 1.0, init_type: str = "batch_average") -> None
  Raises ValueError with message "`num_groups` should be positive, got {num_groups}." when num_groups <= 0
  Raises ValueError with message "`scale` should be positive, got {scale}." when scale <= 0.0
  Raises ValueError with message "Unsupported `init_type` '{init_type}'. Supported values are 'batch_average_once', 'batch_average', and 'random'." when init_type is not one of those three values
  Attributes: num_groups (int), scale (float), init_type (str)

Type: Field (on existing class LoraConfig)
Name: velora_config
Location: src/peft/tuners/lora/config.py
Description: Optional VeloraConfig field added to the existing LoraConfig dataclass. Default is None. When set, enables VeLoRA for all targeted linear layers.
Signature: velora_config: Optional[Union[VeloraConfig, dict]] = None

Type: Module
Name: peft.tuners.lora.velora
Location: src/peft/tuners/lora/velora.py
Description: New module implementing VeLoRA primitives. Must export: VeloraFunction, _compress_activations, _normalize_projection, _reconstruct_activations, _reshape_to_grouped_subtokens.

Type: Function
Name: _reshape_to_grouped_subtokens
Location: src/peft/tuners/lora/velora.py
Signature: _reshape_to_grouped_subtokens(x: torch.Tensor, num_groups: int) -> torch.Tensor
Description: Flattens all leading dimensions of x, pads the last feature dimension with zeros to make it divisible by num_groups, then reshapes to (N, num_groups, ceil(in_features/num_groups)) where N is the product of all leading dimensions. For example, input (2, 5) with num_groups=3 returns shape (2, 3, 2) with zero-padding in the last group.

Type: Function
Name: _compress_activations
Location: src/peft/tuners/lora/velora.py
Signature: _compress_activations(x: torch.Tensor, embed: torch.Tensor, num_groups: int) -> torch.Tensor
Description: Projects grouped subtokens of x onto the embed vector, returning a tensor of shape (N, num_groups) where N is the product of all leading dimensions of x.

Type: Function
Name: _reconstruct_activations
Location: src/peft/tuners/lora/velora.py
Signature: _reconstruct_activations(compressed: torch.Tensor, embed: torch.Tensor, in_features: int, velora_scale: float) -> torch.Tensor
Description: Reconstructs an approximation of the original activations from the compressed representation. Returns a tensor of shape (N, in_features) scaled by velora_scale.

Type: Function
Name: _normalize_projection
Location: src/peft/tuners/lora/velora.py
Signature: _normalize_projection(embed: torch.Tensor) -> torch.Tensor
Description: L2-normalizes a 1D projection vector.

Type: Class
Name: VeloraFunction
Location: src/peft/tuners/lora/velora.py
Description: Custom torch.autograd.Function that implements the VeLoRA forward/backward pass.
Signature:
  apply(x: torch.Tensor, weight: torch.Tensor, bias: Optional[torch.Tensor], embed: torch.Tensor, num_groups: int, velora_scale: float) -> torch.Tensor
  Forward: returns F.linear(x, weight, bias) (identical output to standard linear)
  Backward: computes grad_input as grad_output @ weight (standard); computes grad_weight using _reconstruct_activations of compressed input (X_hat) instead of original X; computes grad_bias as sum over batch dimension

Type: Layer Attributes (on VeLoRA-equipped Linear layers)
Name: lora_velora_embed, lora_velora_initialized
Location: src/peft/tuners/lora/variants.py (VeloraLinearVariant sets them on the Linear module)
Description:
  lora_velora_embed: dict-like (BufferDict), keyed by adapter name. Value is a tensor of shape (ceil(in_features / num_groups),). For init_type="random", initialized with a normalized random vector. For other init types, initialized to zeros until first forward pass.
  lora_velora_initialized: dict keyed by adapter name. Value is bool. False initially for batch_average and batch_average_once; True for random. Becomes True after first forward pass for batch_average_once, and remains True on subsequent passes.
  For init_type="batch_average_once": embed is computed from first forward batch and then frozen (lora_velora_initialized goes False → True and stays True).
  For init_type="batch_average": embed is recomputed from every forward batch.
  For init_type="random": embed is set at initialization time and not updated.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.