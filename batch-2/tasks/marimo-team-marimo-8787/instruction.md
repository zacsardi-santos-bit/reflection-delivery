I'm working with Plotly charts in Marimo and running into several selection correctness issues that I'd like to fix.

*   The _to_numeric_coord function must treat naive datetime objects as UTC-equivalent, producing the same numeric value as a UTC ISO datetime string and a UTC-aware datetime object, regardless of the system timezone setting.

*   The _bar_value_in_selection_range function must accept keyword arguments (trace, point_idx, point_value, selection_min, selection_max) and return False without raising an exception when point_value or the trace base contain non-numeric (e.g. string) values.

*   The _extract_scatter_points_numpy and _extract_scatter_points_fallback functions must accept five positional arguments (fig, xmin, xmax, ymin, ymax) and filter scatter points by both the x and y range, returning only data point vertices that fall within the box (not line segment intersections). Both implementations must produce identical results.

*   Both _extract_scatter_points_numpy and _extract_scatter_points_fallback must handle categorical x and y axes by mapping category labels to integer positions, so that repeated category values share the same axis position and are correctly included or excluded based on the numeric range.

*   When plotly._convert_value receives a selection with a 'range' key, line chart (scatter mode=lines) and scattergl line chart selections must use two-dimensional box filtering, returning only vertices whose x and y coordinates both fall within the selection range. Points whose x coordinate falls in range but y does not must be excluded.

*   When plotly._convert_value receives a selection with a 'lasso' key for a pure line chart (scatter mode=lines), it must return only the data point vertices that fall inside the lasso polygon. Segments that cross the polygon boundary without a vertex inside must not contribute any points.

*   When plotly._convert_value receives a click payload (a dict containing 'points' and 'indices' keys but no 'range' key) on a pure line chart, it must return the 'points' list as-is. The plot.indices property must reflect the provided indices, and plot.ranges must return an empty dict.

*   When plotly._convert_value processes a bar chart selection that already includes explicit points from the frontend, those points must not be duplicated by backend extraction logic. The result must equal exactly the provided explicit points.

*   When plotly._convert_value processes a bar chart selection where the 'points' list contains empty dicts {}, those empty entries must be filtered out. The plot.indices property must be aligned with only the non-empty, valid point entries.

*   The hasPureLineTrace function must accept an array of Plotly trace data objects and return true if any trace has type 'scatter' or 'scattergl' with a mode that includes 'lines' but not 'markers'. It must return false for bar traces, and for scatter/scattergl traces with mode 'markers' or 'lines+markers'.

*   The lineSelectionButtons function must accept a setDragmode callback and return an array of exactly two mode bar button objects. The first button must have name 'line-box-select' and invoke setDragmode with 'select' when clicked. The second must have name 'line-lasso-select' and invoke setDragmode with 'lasso' when clicked.

*   The mergeModeBarButtonsToAdd function must accept two arrays of ModeBarButton values (strings or objects) and return a merged array that deduplicates string buttons while preserving custom object buttons even when a string with the same name exists.

*   The shouldHandleClickSelection function must accept an array of plot data points and return true when points include bar, heatmap, or histogram trace types, or scatter/scattergl traces without an explicit mode. It must return false when all scatter/scattergl points have mode set to 'markers'.

*   The extractIndices function must prefer pointIndex if present, fall back to pointNumber, and finally fall back to values in the pointNumbers array, filtering out NaN and non-finite values from pointNumbers. Points with only Infinity in pointNumbers must contribute no index.

*   The extractPoints function must infer x and y coordinates from fullData arrays (using pointNumber as the array index) for scatter line traces. For heatmap traces it must return only x, y, and z fields. It must also parse hovertemplate labels from customdata while retaining the inferred coordinate fields.

*   When PlotlyComponent receives a click event on a bar trace, it must invoke setValue with an updater function. When that updater is called with the current value, it must return an object with keys: selections (empty array), points (array containing the clicked point with x, y, curveNumber, pointNumber, pointIndex), indices (array of the point's pointIndex), and range (undefined).


*   Interface details: ## Python — `marimo/_plugins/ui/_impl/plotly.py`

Type: Function
Name: _to_numeric_coord
Location: marimo/_plugins/ui/_impl/plotly.py
Signature: _to_numeric_coord(value) -> float | int
Description: Converts a value (datetime, aware datetime, or ISO datetime string) to a numeric coordinate for range comparisons. Naive datetime objects must be treated as UTC, producing the same result as a UTC ISO string and a UTC-aware datetime regardless of system timezone.

Type: Function
Name: _bar_value_in_selection_range
Location: marimo/_plugins/ui/_impl/plotly.py
Signature: _bar_value_in_selection_range(trace, point_idx, point_value, selection_min, selection_max) -> bool
Description: Checks whether a bar chart data point falls within the selection range. Must return False (not raise) when point_value or trace base are non-numeric (e.g. string) values.

Type: Function
Name: _extract_scatter_points_numpy
Location: marimo/_plugins/ui/_impl/plotly.py
Signature: _extract_scatter_points_numpy(fig, xmin, xmax, ymin, ymax) -> list
Description: Extracts scatter data points (numpy-accelerated path) whose vertices fall within the bounding box defined by xmin, xmax, ymin, ymax. Must handle categorical axes by converting category labels to integer positions. Returns an empty list when no vertex falls in the box.

Type: Function
Name: _extract_scatter_points_fallback
Location: marimo/_plugins/ui/_impl/plotly.py
Signature: _extract_scatter_points_fallback(fig, xmin, xmax, ymin, ymax) -> list
Description: Extracts scatter data points (pure-Python fallback path) whose vertices fall within the bounding box defined by xmin, xmax, ymin, ymax. Must produce identical results to _extract_scatter_points_numpy, including categorical axis support.

Type: Class
Name: plotly
Location: marimo/_plugins/ui/_impl/plotly.py
Description: Marimo UI element wrapping a Plotly figure with interactive selection support.
Signature: _convert_value(value: dict) -> list

---

## TypeScript — `frontend/src/plugins/impl/plotly/selection.ts`

Type: Function
Name: hasPureLineTrace
Location: frontend/src/plugins/impl/plotly/selection.ts
Signature: hasPureLineTrace(traces: Plotly.Data[]) -> boolean
Description: Returns true if any trace in the array is of type "scatter" or "scattergl" with a mode that includes "lines" but not "markers". Returns false for bar traces, and for scatter/scattergl traces with mode "markers" or "lines+markers".

Type: Function
Name: lineSelectionButtons
Location: frontend/src/plugins/impl/plotly/selection.ts
Signature: lineSelectionButtons(setDragmode: (mode: string) => void) -> ModeBarButton[]
Description: Returns an array of exactly two mode bar button objects. Index 0 has name "line-box-select" and calls setDragmode("select") when clicked. Index 1 has name "line-lasso-select" and calls setDragmode("lasso") when clicked.

Type: Type Alias
Name: ModeBarButton
Location: frontend/src/plugins/impl/plotly/selection.ts
Signature: type ModeBarButton = string | { name: string; title: string; icon: object; click: (gd: Plotly.PlotlyHTMLElement, ev: MouseEvent) => void }
Description: Exported type representing either a named string button or a custom mode bar button object with name, title, icon, and click handler.

Type: Function
Name: mergeModeBarButtonsToAdd
Location: frontend/src/plugins/impl/plotly/selection.ts
Signature: mergeModeBarButtonsToAdd(existing: ModeBarButton[], add: ModeBarButton[]) -> ModeBarButton[]
Description: Merges two arrays of ModeBarButton values, deduplicating string buttons while preserving custom object buttons. String buttons that appear in both arrays are deduplicated (first occurrence wins); custom objects are always retained.

Type: Function
Name: shouldHandleClickSelection
Location: frontend/src/plugins/impl/plotly/selection.ts
Signature: shouldHandleClickSelection(points: Plotly.PlotDatum[]) -> boolean
Description: Returns true when plot points belong to trace types that support direct click selection (bar, heatmap, histogram, or scatter/scattergl without explicit mode). Returns false when all scatter/scattergl points have mode set to "markers". Replaces the removed extractClickSelection function.

Type: Function
Name: extractIndices
Location: frontend/src/plugins/impl/plotly/selection.ts
Signature: extractIndices(points: Plotly.PlotDatum[]) -> number[]
Description: Extracts a flat list of point indices. Prefers pointIndex if present, falls back to pointNumber, then to values in the pointNumbers array. NaN and non-finite (Infinity) values from pointNumbers are excluded.

Type: Function
Name: extractPoints
Location: frontend/src/plugins/impl/plotly/selection.ts
Signature: extractPoints(points: Plotly.PlotDatum[]) -> object[]
Description: Extracts serializable point data. For scatter line traces (fullData.mode includes "lines"), infers x and y from fullData.x/y arrays at the pointNumber index, and sets pointIndex equal to pointNumber. Parses hovertemplate label fields from customdata. For heatmap traces, returns only x, y, and z fields (hovertemplate is ignored).

---

## TypeScript — `frontend/src/plugins/impl/plotly/PlotlyPlugin.tsx`

Type: Component
Name: PlotlyComponent
Location: frontend/src/plugins/impl/plotly/PlotlyPlugin.tsx
Signature: PlotlyComponent(props: { figure: { data: unknown[]; layout: Record<string, unknown>; frames: unknown[] | null }; value: unknown; setValue: Setter<unknown>; host: HTMLElement; config: Record<string, unknown> }) -> JSX.Element
Description: React component that renders a Plotly chart. When a bar trace is clicked, it calls setValue with an updater function. The updater function, given the current value, returns an object with shape: { selections: [], points: [{ x, y, curveNumber, pointNumber, pointIndex }], indices: [pointIndex], range: undefined }.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.