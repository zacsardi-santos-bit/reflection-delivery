Update the nvidia-smi input plugin to support parsing power draw data from older Quadro-series GPU models that use a different XML element for power reporting. Ensure that the plugin can handle both the older and newer XML formats to provide complete power metrics for all GPU models.

*   Update the v12 schema types file (`plugins/inputs/nvidia_smi/schema_v12/types.go`):
    *   Add a struct to map the `<power_readings>` XML element.
    *   Include a `PowerDraw` field mapped to the `<power_draw>` child element.

*   Modify the v12 schema parser file (`plugins/inputs/nvidia_smi/schema_v12/parser.go`):
    *   Implement logic to read `power_draw` from both `<power_readings>` and `<gpu_power_readings>` XML elements.

*   Ensure the plugin emits a metric named 'nvidia_smi' with specific tags and fields:
    *   Tags: 
        *   arch='Pascal'
        *   compute_mode='Default'
        *   index='0'
        *   name='Quadro P2000'
        *   pstate='P8'
        *   uuid='GPU-396caaed-39ca-3199-2e68-717cdb786ec6'
    *   Fields:
        *   Float64 field: `power_draw` extracted as 4.61.
        *   Integer fields: 
            *   clocks_current_graphics=139
            *   clocks_current_memory=405
            *   clocks_current_sm=139
            *   clocks_current_video=544
            *   encoder_stats_average_fps=0
            *   encoder_stats_average_latency=0
            *   encoder_stats_session_count=0
            *   fbc_stats_average_fps=0
            *   fbc_stats_average_latency=0
            *   fbc_stats_session_count=0
            *   fan_speed=46
            *   memory_free=5051
            *   memory_reserved=66
            *   memory_total=5120
            *   memory_used=1
            *   pcie_link_gen_current=1
            *   pcie_link_width_current=8
            *   temperature_gpu=34
            *   utilization_gpu=0
            *   utilization_memory=0
            *   utilization_encoder=0
            *   utilization_decoder=0
        *   String fields:
            *   cuda_version='12.0'
            *   display_active='Disabled'
            *   display_mode='Disabled'
            *   driver_version='525.125.06'
            *   serial='0322218049033'
            *   vbios_version='86.06.3F.00.30'

*   Create a test XML fixture file at `plugins/inputs/nvidia_smi/testdata/quadro-p2000-v12.xml`:
    *   Ensure it contains valid nvidia-smi XML output for a Quadro P2000 GPU with a 'power_readings' element reporting power_draw as '4.61 W'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.