Update the chart interaction logic to correctly highlight data points and position tooltips when the chart is not at the top of the page. Adjust the coordinate calculations to account for the chart's vertical and horizontal offsets within the page layout.

*   Modify the `MousePointer` interface in `src/chart/generateCategoricalChart.tsx`:
    *   Add a `currentTarget` field typed as `Pick<HTMLElement, 'offsetTop' | 'offsetLeft'>`.
    *   Ensure it includes the existing `pageX` and `pageY` number fields.

*   Update the `selectChartCoordinates` function in `src/state/selectors/containerSelectors.ts`:
    *   Compute `chartX` as `Math.round(event.pageX - event.currentTarget.offsetLeft)`.
    *   Compute `chartY` as `Math.round(event.pageY - event.currentTarget.offsetTop)`.
    *   Use the `currentTarget` offsets from the `MousePointer` directly for these calculations.

*   Ensure `selectActivePropsFromMousePointer` behaves as follows:
    *   Located in `src/state/selectors/`.
    *   Accepts a `MousePointer` with `pageX=10`, `pageY=10`, `currentTarget={offsetTop:1, offsetLeft:3}`.
    *   Returns `{ activeCoordinate: { x: 5, y: 9 }, activeIndex: '0' }`.
    *   The `y` value should reflect `pageY` minus `currentTarget.offsetTop` (10 - 1 = 9).
    *   The `x` value should be the chart-coordinate of the nearest data point.
    *   Maintain referential stability: multiple calls with the same input must return the same object reference.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.