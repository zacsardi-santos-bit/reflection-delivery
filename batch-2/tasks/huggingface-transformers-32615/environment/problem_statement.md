## Add FalconMamba Model Support to Transformers

### Description

The FalconMamba model family is a new large language model architecture based on the Mamba state space design, released by TII UAE. It achieves competitive performance with leading open-weight models at the 7B scale while offering significantly faster inference and lower memory usage for long sequences due to its non-attention-based design.

Currently, the Transformers library does not support FalconMamba, so developers cannot load the Falcon-Mamba-7B pretrained checkpoint through the standard API. There is no way to use FalconMamba for text generation, fine-tuning, or pipeline integration via Transformers.

### Expected Behavior

- A configuration class for FalconMamba should be available and importable from the library, including attributes for hidden size, number of hidden layers, state size, convolution kernel size, and time-step bounds
- A base FalconMamba model should be available for feature extraction, returning hidden states
- A causal language modeling variant should support text generation, loss computation, and backward passes
- Both models should support stateful caching so that sequential token processing is equivalent to processing the full sequence at once
- The architecture should be registered with the auto-model system so it can be discovered automatically from pretrained checkpoints
- Both models should integrate with the text-generation and feature-extraction pipeline types

### Why This Matters

Users want to experiment with and deploy the FalconMamba architecture, which offers an efficient alternative to transformer-based LLMs. Without library support, they cannot leverage existing infrastructure for fine-tuning, quantization, or compiled inference that the library already provides for other models.
