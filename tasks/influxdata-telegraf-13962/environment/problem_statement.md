## Description

The nvidia-smi GPU monitoring plugin does not collect power draw data from older NVIDIA GPU models (such as Quadro-series cards) that report power consumption using a different XML element name than newer GPU architectures. As a result, users monitoring these older GPUs receive metrics with the power draw field missing entirely, even though the GPU itself does expose this information.

## Expected Behavior

- When monitoring older Quadro-series GPU models that report power under a different section name in their nvidia-smi XML output, the plugin should correctly extract and report the power draw value.
- The collected metric should include the power draw field with the correct numeric value, alongside all other standard GPU metrics (clocks, memory, utilization, temperature, fan speed, etc.).
- The plugin should handle both the older and the newer power reporting XML formats, so that mixed fleets of GPUs are all supported.

## Why This Matters

Operators running mixed GPU infrastructure that includes older workstation-class GPUs alongside newer data center GPUs cannot currently get complete power metrics from the older hardware. This gap makes it impossible to accurately track power consumption across an entire fleet using this plugin alone.
