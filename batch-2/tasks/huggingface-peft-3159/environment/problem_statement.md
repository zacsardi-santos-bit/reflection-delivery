## Description

Standard low-rank adaptation (LoRA) fine-tuning stores the full input activations of each adapted layer during the forward pass so they can be used to compute weight gradients during backpropagation. For large models with wide hidden dimensions, these cached activations can account for a significant fraction of peak GPU memory, limiting the batch sizes or model sizes that fit on a given device.

It would be useful to have a LoRA variant that reduces this memory cost by compressing the input activations into a compact representation during the forward pass and then reconstructing an approximation during the backward pass. The compression should be configurable: users should be able to control how many groups the input features are split into, a scale factor applied during reconstruction, and how the compression projection vector is initialized (randomly at startup, once from the first training batch, or freshly from each training batch).

## Expected Behavior

- A new configuration class allows users to enable and configure this memory-efficient LoRA variant by attaching it to the standard LoRA configuration.
- The resulting fine-tuning should use significantly less memory for stored intermediate activations compared to standard LoRA, while still allowing normal gradient-based training.
- The adapted layers should support the three projection initialization strategies, with different update policies depending on the chosen strategy.
- The configuration class should validate its inputs and raise descriptive errors for invalid values (non-positive group count, non-positive scale, or unsupported initialization type).

## Why This Matters

Memory is one of the primary bottlenecks when fine-tuning large models. This feature lets practitioners fine-tune with larger batches or on devices with less memory, closing the gap between LoRA and gradient-checkpointing approaches while retaining better training throughput.
