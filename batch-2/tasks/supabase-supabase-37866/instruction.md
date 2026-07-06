Implement a React hook named `useChartHoverState` to manage hover interactions across multiple chart instances, with optional synchronization for hover and tooltip states. Ensure these preferences persist across page reloads and handle edge cases gracefully.

*   Create the `useChartHoverState` hook in `apps/studio/components/ui/Charts/useChartHoverState.ts`.
    *   Initialize with default state: `hoveredIndex` as null, `hoveredChart` as null, `isHovered` as false, `isCurrentChart` as false, `syncHover` as false, and `syncTooltip` as false.
    *   Read `syncHover` from localStorage key 'supabase-chart-hover-sync-enabled' and `syncTooltip` from 'supabase-chart-tooltip-sync-enabled'.
        *   If values are corrupted or non-JSON-parseable, default both to false and emit a console warning.
*   Implement hover state management:
    *   `setHover(index: number | null)`: 
        *   When `syncHover` is false, update only the local state.
        *   When `syncHover` is true, update the global state so all instances reflect the change.
        *   Ensure `isCurrentChart` is true only for the instance that called `setHover`.
    *   `clearHover()`: 
        *   Reset hover state locally or globally based on `syncHover`.
*   Implement synchronization controls:
    *   `setSyncHover(enabled: boolean)`: 
        *   Update `syncHover` for all instances and persist to localStorage.
        *   Disabling `syncHover` also disables `syncTooltip`.
        *   Ensure no state update occurs if the current state matches the requested state.
    *   `setSyncTooltip(enabled: boolean)`: 
        *   Enable both `syncTooltip` and `syncHover`, persisting both states to localStorage.
*   Handle localStorage errors:
    *   If saving to localStorage fails, update in-memory state and log a warning with the message 'Failed to save chart hover sync setting to localStorage:' followed by the error.
*   Ensure state consistency:
    *   `isHovered` should be true for all instances with a non-null `hoveredIndex`.
    *   `isCurrentChart` should be true only for the instance whose `chartId` matches `hoveredChart`.
    *   Rapid sequential calls to `setHover` and `clearHover` should reflect only the final state.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.