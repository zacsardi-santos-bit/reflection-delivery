Implement a fix for the scatter chart rendering issue in Recharts where incorrect scatter points flash briefly when the data key or dataset changes. Ensure that scatter points are only computed and rendered once the chart's internal state is synchronized with the new configuration.

Requirements:
*   Modify the `selectScatterPoints` function located in `src/state/selectors/scatterSelectors.ts` to handle synchronization checks.
    *   Ensure `selectScatterPoints` returns `undefined` on its first invocation if the scatter component has not registered its settings in the chart state.
    *   Ensure `selectScatterPoints` is called exactly 3 times when a `ScatterChart` with a `Scatter` child renders:
        *   First call: return `undefined`.
        *   Second call: return the computed scatter points array.
        *   Third call: return the same computed scatter points array.
    *   Ensure `selectScatterPoints` produces referentially stable results by returning the same object reference when invoked with the same arguments after registration.
    *   Gate scatter point computation with a synchronization check:
        *   If `scatterSettings` (dataKey and data) do not match any registered scatter graphical item, return `undefined`.

*   Introduce a new internal selector to perform the synchronization check before proceeding with point computation.
*   Maintain the existing function signature:
    *   `selectScatterPoints(state: RechartsRootState, xAxisId: AxisId, yAxisId: AxisId, zAxisId: AxisId, scatterSettings: ResolvedScatterSettings, cells: unknown, isPanorama: boolean) -> ReadonlyArray<ScatterPointItem> | undefined`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.