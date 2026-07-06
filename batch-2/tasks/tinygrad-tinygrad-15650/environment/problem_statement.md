## Description

The AMD GPU profiling timeline analysis crashes for RDNA3 and RDNA4 hardware when certain kernel profiles are analyzed. The root cause is that the timeline generator emits instruction events from other SIMD units (which share the same execution block as the wave being traced) as their own separate labeled stream. The analysis code previously had special logic to skip these events, but that skip is being removed so these events must instead be incorporated into the normal wave-level stream where they can be properly identified and excluded from instruction counting.

## Expected Behavior

- The timeline generator must not produce events with a device label that is neither a WAVE row nor an EXEC row. All emitted range events must belong to one of those two categories.
- Instructions from other SIMDs sharing an execution block should be folded into the wave-level stream, labeled by their instruction name in a way that allows the analysis to exclude them from the total instruction count.
- The count of execution events must equal the count of counted wave instructions after all exclusions are applied, for every kernel trace on RDNA3 and RDNA4 hardware.
- The matrix-multiply execution overlap detection on RDNA4 should only apply to the dedicated WMMA execution device row and must raise an error whose message clearly identifies it as a WMMA overlap, including the device name and the conflicting timestamps.

## Why This Matters

Without this fix, profiling any RDNA3 or RDNA4 kernel that involves multi-SIMD execution results in a hard crash during timeline analysis, making it impossible to validate or visualize GPU execution timing for a large class of real-world kernels.
