I'm having a problem with how point selections work on Plotly scatter charts in my notebook.

*   When _convert_value is called on a plotly UIElement whose figure contains only scatter/scattergl traces in 'markers' mode, and the incoming selection already contains explicit point payloads from Plotly (with curveNumber, pointIndex, etc.), those exact points must be returned unchanged — the result must NOT be expanded to include other data points that share the same x-coordinate.

*   When _convert_value is called and the figure contains only scatter/scattergl traces in 'lines' mode (no markers), the method must fall back to extracting all data points whose x-values fall within the selection range, because Plotly does not provide point-level data for pure line traces.

*   When _convert_value is called on a figure containing a mix of 'markers' traces and 'lines' traces, the result must combine: (a) the explicit Plotly points for marker traces, and (b) the range-extracted points for line-only traces. The two strategies are applied per-trace, not globally.

*   When _convert_value is called on a mixed bar + scatter figure where Plotly provides an explicit scatter point in the selection (e.g. {"x": 1, "y": 10, "curveNumber": 0, "pointIndex": 1}), that exact scatter point object must appear in the returned list alongside the bar chart results.

*   The 'indices' property of a plotly UIElement must return the indices list from the most recent selection data, reflecting which point indices Plotly reported as selected (e.g. [1] when the selection had indices: [1]).


*   Interface details: Type: Class
Name: plotly
Location: marimo/_plugins/ui/_impl/plotly.py
Description: UIElement wrapping a Plotly figure with interactive selection support.
Signature:
  _convert_value(self, value: dict[str, Any]) -> list[dict[str, Any]]
    Converts a raw Plotly selection payload into a list of selected data-point dicts.
    The `value` dict has keys:
      - "range": dict with "x" and "y" sub-keys describing the selection box
      - "points": list of point dicts (each may include "x", "y", "curveNumber", "pointIndex", and custom axis names)
      - "indices": list of int point indices
    Returns a list of point dicts corresponding to the selected data points.
  indices (property) -> list[int]
    Returns the list of point indices from the most recently processed selection,
    mirroring the "indices" field of the selection payload.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.