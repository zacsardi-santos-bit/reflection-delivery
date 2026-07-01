Implement the core functionality for the SQL query receiver to execute SQL queries against a database, convert each result row into an OpenTelemetry metric, and stream these metrics to a downstream consumer. Expand the metric configuration to support additional options and ensure comprehensive validation of configurations at startup.

Requirements:

*   Rename the existing `Metric` struct to `MetricCfg` and extend it with new fields: `Monotonic`, `DataType`, `ValueType`, `Aggregation`, `Description`, and `Unit`. Update the `Query` struct's `Metrics` field to use `MetricCfg`.
*   Define three new string enum types:
    *   `MetricDataType`: values 'gauge', 'sum'.
    *   `MetricValueType`: values 'int', 'double'.
    *   `MetricAggregation`: values 'cumulative', 'delta'.
    *   Implement a `Validate()` method for each type to return a typed error for unrecognized values.
*   Implement `Config.Validate()` to check for non-empty `Driver`, `DataSource`, and `Queries`, returning specific error messages for each missing field.
*   Implement `Query.Validate()` to ensure `SQL` and `Metrics` are not empty, collecting errors via `multierr`.
*   Implement `MetricCfg.Validate()` to collect all validation errors using `multierr`, checking for empty `MetricName` and `ValueColumn`, unsupported types, and incompatible combinations.
*   Define a `dbClient` interface with a method `metricRows(ctx context.Context) ([]metricRow, error)`. Define `metricRow` as `map[string]string`.
*   Implement a `scraper` struct with fields `dbProviderFunc`, `client`, and `query`. The `Start` method should call `dbProviderFunc` and handle errors. The `Scrape` method should call `client.metricRows` and handle errors.
*   Ensure `Scrape` converts each row in the result set to a `pmetric.Metric` per `MetricCfg`, handling `DataType` and `Aggregation` appropriately.
*   Handle row conversion errors in `Scrape`, collecting all errors and returning them in a specific format.
*   Set columns listed in `AttributeColumns` as string attributes on each data point. Apply `Description` and `Unit` to the metric descriptor.
*   Implement `createReceiverFunc` to create a metrics receiver using a DB-opener and client factory function, registering it in the factory.
*   Ensure the following testdata YAML files exist in `receiver/sqlqueryreceiver/testdata/`:
    *   `config.yaml` (valid configuration)
    *   `config-invalid-datatype.yaml`
    *   `config-invalid-valuetype.yaml`
    *   `config-invalid-aggregation.yaml`
    *   `config-invalid-missing-metricname.yaml`
    *   `config-invalid-missing-valuecolumn.yaml`
    *   `config-invalid-missing-sql.yaml`
    *   `config-invalid-missing-queries.yaml`
    *   `config-invalid-missing-driver.yaml`
    *   `config-invalid-missing-metrics.yaml`
    *   `config-invalid-missing-datasource.yaml`
    *   `config-unnecessary-aggregation.yaml`
    *   `config-invalid-multierr.yaml`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.