I'm working on optimizing Qwen3 models with ONNX Runtime's transformer optimizer and running into a fusion gap.

*   The create_qwen3_decoder_layer function must accept three new optional boolean parameters: include_rope (default False), include_expand_in_inv_freq (default False), and inv_freq_as_graph_input (default False).

*   When include_rope=True, the generated model must include on-the-fly RoPE computation nodes (a MatMul of inv_freq and position IDs, followed by Transpose, Concat, Cos/Sin, scaling Mul, and Unsqueeze), a 'position_ids' graph input of shape (batch_size, seq_len) with INT64 dtype, and rotate_half application to both Q and K tensors using dynamic Shape→Gather→Div→Cast→Cast→Unsqueeze slice-index computation.

*   When include_expand_in_inv_freq=True (combined with include_rope=True), the inv_freq path must additionally include Cast→Expand→Where nodes before the MatMul, adding corresponding initializers for expand_shape, where_cond (boolean), and where_zero (float).

*   When inv_freq_as_graph_input=True (combined with include_rope=True), the inv_freq must be provided as a graph input named 'rope_inv_freq' with shape (1, head_dim // 2, 1) and FLOAT dtype instead of as an initializer.

*   The Qwen3 transformer optimizer (model_type='qwen3') must recognize and fuse the on-the-fly RoPE pattern into RotaryEmbedding nodes. After optimization of a model with include_rope=True, the resulting graph must contain exactly 2 nodes with op_type 'RotaryEmbedding' (one for Q, one for K) and exactly 4 nodes with op_type 'SimplifiedLayerNormalization'.

*   The optimizer must handle the inv_freq expand variant (include_expand_in_inv_freq=True) and still produce exactly 2 RotaryEmbedding nodes after optimization.

*   When inv_freq is provided as a dynamic graph input rather than a constant initializer (inv_freq_as_graph_input=True), the optimizer must gracefully skip fusion and produce 0 RotaryEmbedding nodes without crashing.

*   After successful fusion, the optimized model must contain initializers named exactly 'cos_cache' and 'sin_cache'. Both must have the same shape (max_seq_len, head_dim // 2), where max_seq_len must be at least 1001 (positions 0, 1, 7, 100, and 1000 are validated) and the second dimension must equal head_dim // 2.

*   The values in cos_cache and sin_cache must satisfy: cos_cache[pos, :] = cos(pos * inv_freq) * scaling and sin_cache[pos, :] = sin(pos * inv_freq) * scaling, where inv_freq is the 1D flattened inverse frequency array and scaling is the attention scaling factor (1.0 in tests). Numerical correctness is verified with relative tolerance 1e-6 at positions 0, 1, 7, 100, and 1000.


*   Interface details: Type: Function
Name: create_qwen3_decoder_layer
Location: onnxruntime/test/python/transformers/qwen3_model_generator.py
Signature: create_qwen3_decoder_layer(hidden_size=64, num_heads=8, num_kv_heads=2, batch_size=1, seq_len=4, include_rope=False, include_expand_in_inv_freq=False, inv_freq_as_graph_input=False) -> onnx.ModelProto
Description: Generates a single Qwen3 decoder layer ONNX model for testing. The three new parameters control whether on-the-fly RoPE nodes are included (include_rope), whether the inv_freq path uses Cast→Expand→Where nodes (include_expand_in_inv_freq), and whether inv_freq is a graph input rather than an initializer (inv_freq_as_graph_input). When include_rope=True, a 'position_ids' input (INT64, shape [batch_size, seq_len]) is added; when inv_freq_as_graph_input=True, a 'rope_inv_freq' input (FLOAT, shape [1, head_dim//2, 1]) is also added.

Type: Function (helper, called internally by create_qwen3_decoder_layer)
Name: _on_the_fly_rope_nodes
Location: onnxruntime/test/python/transformers/qwen3_model_generator.py
Signature: _on_the_fly_rope_nodes(prefix: str, head_dim: int, include_expand: bool = False) -> list
Description: Builds the on-the-fly RoPE computation node list: Unsqueeze→Cast→(optional Cast→Expand→Where)→MatMul→Transpose→Concat→Cos→Sin→Mul(scaling)→Unsqueeze. Outputs nodes for the 'cos_out' and 'sin_out' tensors used as inputs to the rotate_half application.

Type: Function (helper, called internally by create_qwen3_decoder_layer)
Name: _on_the_fly_rope_initializers
Location: onnxruntime/test/python/transformers/qwen3_model_generator.py
Signature: _on_the_fly_rope_initializers(prefix: str, head_dim: int, batch_size: int = 1, include_expand: bool = False, inv_freq_as_graph_input: bool = False) -> list
Description: Generates initializer tensors for the on-the-fly RoPE computation. Includes the inv_freq weight (shape [1, head_dim//2, 1], computed as 1.0 / (10000 ** (arange(0, head_dim, 2) / head_dim))), scaling (float scalar, 1.0), axis constants, and optionally expand_shape/where_cond/where_zero for the expand variant. inv_freq is omitted when inv_freq_as_graph_input=True.

Type: Function (helper, called internally by create_qwen3_decoder_layer)
Name: _rotate_half_nodes
Location: onnxruntime/test/python/transformers/qwen3_model_generator.py
Signature: _rotate_half_nodes(prefix: str, input_name: str, output_name: str, cos_name: str, sin_name: str) -> list
Description: Builds the rotate_half + apply_rotary_pos_emb node pattern: Shape→Gather→Div→Cast→Cast→Unsqueeze for dynamic slice index computation, two Slice nodes for x1/x2, Neg, Concat for rotate_half, two Mul nodes for x*cos and rotate_half*sin, and a final Add for the embedded output.

Type: Function (helper, called internally by create_qwen3_decoder_layer)
Name: _rotate_half_initializers
Location: onnxruntime/test/python/transformers/qwen3_model_generator.py
Signature: _rotate_half_initializers(prefix: str) -> list
Description: Generates the constant initializer tensors needed for dynamic Slice index computation in rotate_half: dim_idx (INT64 scalar = 3), two (INT64 scalar = 2), unsq_axis ([0]), zero_start ([0]), large_end ([9223372036854775807] = INT64_MAX), slice_axis ([-1]), one_step ([1]).

Note on production implementation: The tests require the Qwen3 optimizer (accessed via optimize_model with model_type="qwen3") to fuse the on-the-fly RoPE pattern. The implementation lives in onnxruntime/python/tools/transformers/fusion_rotary_attention.py in the FusionRotaryEmbeddings class. After fusion, the optimized model must contain initializers named exactly "cos_cache" and "sin_cache" with shape (max_seq_len, head_dim // 2), where max_seq_len must be at least 1001. Values must satisfy: cos_cache[pos, :] = cos(pos * inv_freq) * scaling and sin_cache[pos, :] = sin(pos * inv_freq) * scaling.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.