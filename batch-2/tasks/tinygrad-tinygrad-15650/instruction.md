I'm running into failures in the AMD GPU profiling timeline analysis for RDNA3 and RDNA4 hardware.

*   The sqtt_timeline function must not produce ProfileRangeEvent objects with a device value of 'OTHER_SIMD' or any device string that does not contain either 'EXEC' or 'WAVE'. Every ProfileRangeEvent emitted must have a device field whose string contains at least one of those two substrings.

*   Instructions from other SIMD units that share an execution block (previously emitted as a separate 'OTHER_SIMD' stream) must instead be placed on WAVE-row events. These events' name.display_name must start with the prefix 'OTHER_' so that they can be identified and excluded from instruction counting.

*   After processing a complete kernel trace, the count of non-ALT execution events (ProfileRangeEvent objects where 'EXEC' is in device and 'ALT' is not in name.display_name) must equal the count of counted wave instruction events (ProfileRangeEvent objects where 'WAVE' is in device, display_name is not in the excluded set {IMMEDIATE, IMMEDIATE_MASK, JUMP, JUMP_NO, MESSAGE, BARRIER, BARRIER_SIGNAL, WAVEEND, WAVERDY}, and display_name does not start with 'OTHER_').

*   The RDNA4 WMMA execution overlap check must only apply to ProfileRangeEvent objects where device is exactly 'ALUEXEC:0 WMMA'. When such an overlap is detected (a new event starts before the previous one ends), a RuntimeError must be raised with message formatted as 'WMMA exec overlaps in {device}: {st} {et}.'.


*   Interface details: Type: Function
Name: sqtt_timeline
Location: tinygrad/viz/serve.py
Signature: sqtt_timeline(data: bytes, lib: bytes, target: str) -> Generator[ProfileEvent, None, None]
Description: Parses a raw SQTT trace blob and yields profiling events. Must not emit any ProfileRangeEvent whose device field does not contain "EXEC" or "WAVE". Instructions from other SIMD units that previously mapped to device "OTHER_SIMD" must instead be placed on a WAVE row (device string contains "WAVE"), and those events' name.display_name must start with the prefix "OTHER_" so they can be identified and excluded from instruction counting. The function must not produce events with device == "OTHER_SIMD" or any device label outside the EXEC/WAVE categories.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.