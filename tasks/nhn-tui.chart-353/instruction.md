Fix the line and area chart components by adjusting the line width and ensuring the "show dot" option correctly renders dots. Implement the following changes to meet the expected behavior and update type definitions accordingly.

*   Adjust the line width:
    *   Set the `lineWidth` property to 3 for series line models in both `AreaSeries` and `LineSeries`.

*   Ensure dot markers are rendered:
    *   Modify the `models` object in `AreaSeries` and `LineSeries` components to always include a `dot` key.
        *   When `showDot` is false or not set, `dot` must be an empty array.
        *   When `showDot` is true, populate `dot` with circle model objects for each data point.
    *   For `AreaSeries`:
        *   Include fields in each circle model: `color` (rgba string), `index` (0-based), `name` (series name), `radius` (6), `seriesIndex` (0-based), `style` (['default']), `type` ('circle'), `x` (pixel coordinate), `y` (pixel coordinate).
    *   For `LineSeries`:
        *   Include fields in each circle model: `color` (rgba string), `radius` (6), `seriesIndex` (0-based), `style` (['default']), `type` ('circle'), `x` (pixel coordinate), `y` (pixel coordinate).
        *   Exclude `name` and `index` fields.

*   Update TypeScript type definitions in `types/components/series.d.ts`:
    *   Add `dot: CircleModel[]` to `AreaSeriesModels` and `LineSeriesModels`.
    *   Include an optional `name?: string` in `CircleModel` to support series names in area series.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.