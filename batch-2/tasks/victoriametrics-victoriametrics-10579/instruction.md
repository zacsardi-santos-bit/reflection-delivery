I'm working on VictoriaMetrics and noticed that the Graphite tag series registration endpoints are currently creating new time series as a side effect when called.

*   The HTTP endpoint for registering a single Graphite tag series (POST to /graphite/tags/tagSeries in single-node mode, and POST to /select/<tenant>/graphite/tags/tagSeries in cluster mode) must return HTTP 501 Not Implemented.

*   The HTTP endpoint for registering multiple Graphite tag series (POST to /graphite/tags/tagMultiSeries in single-node mode, and POST to /select/<tenant>/graphite/tags/tagMultiSeries in cluster mode) must return HTTP 501 Not Implemented.

*   When the tagSeries endpoint is called, no new time series must be created as a side effect.

*   When the tagMultiSeries endpoint is called with multiple records, no new time series must be created as a side effect.

*   The GraphiteTagsTagSeries method on both Vmselect and Vmsingle test helper types must have no return value and must validate that the server returns HTTP 501 Not Implemented.

*   The GraphiteTagsTagMultiSeries method on both Vmselect and Vmsingle test helper types must have no return value and must validate that the server returns HTTP 501 Not Implemented.

*   The PrometheusQuerier interface must declare GraphiteTagsTagSeries with no return value, and GraphiteTagsTagMultiSeries with no return value.


*   Interface details: Type: Interface
Name: PrometheusQuerier
Location: apptest/model.go
Description: Interface that includes Graphite tag series registration methods. Both methods must have no return value.
Signature: GraphiteTagsTagSeries(t *testing.T, record string, opts QueryOpts)
Signature: GraphiteTagsTagMultiSeries(t *testing.T, records []string, opts QueryOpts)

Type: Method
Name: GraphiteTagsTagSeries
Location: apptest/vmselect.go
Description: Test helper that sends a POST to /select/<tenant>/graphite/tags/tagSeries and validates the server returns HTTP 501 Not Implemented. Must have no return value.
Signature: (app *Vmselect) GraphiteTagsTagSeries(t *testing.T, record string, opts QueryOpts)

Type: Method
Name: GraphiteTagsTagMultiSeries
Location: apptest/vmselect.go
Description: Test helper that sends a POST to /select/<tenant>/graphite/tags/tagMultiSeries and validates the server returns HTTP 501 Not Implemented. Must have no return value.
Signature: (app *Vmselect) GraphiteTagsTagMultiSeries(t *testing.T, records []string, opts QueryOpts)

Type: Method
Name: GraphiteTagsTagSeries
Location: apptest/vmsingle.go
Description: Test helper that sends a POST to /graphite/tags/tagSeries and validates the server returns HTTP 501 Not Implemented. Must have no return value.
Signature: (app *Vmsingle) GraphiteTagsTagSeries(t *testing.T, record string, opts QueryOpts)

Type: Method
Name: GraphiteTagsTagMultiSeries
Location: apptest/vmsingle.go
Description: Test helper that sends a POST to /graphite/tags/tagMultiSeries and validates the server returns HTTP 501 Not Implemented. Must have no return value.
Signature: (app *Vmsingle) GraphiteTagsTagMultiSeries(t *testing.T, records []string, opts QueryOpts)

Type: HTTP Handler
Name: RequestHandler (case "/tags/tagSeries")
Location: app/vmselect/main.go
Description: HTTP request handler case for the /tags/tagSeries path. Must return HTTP 501 Not Implemented instead of delegating to the Graphite tag registration handler. No new time series should be created.

Type: HTTP Handler
Name: RequestHandler (case "/tags/tagMultiSeries")
Location: app/vmselect/main.go
Description: HTTP request handler case for the /tags/tagMultiSeries path. Must return HTTP 501 Not Implemented instead of delegating to the Graphite tag multi-series registration handler. No new time series should be created.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.