Implement support for dynamic data sources in MOSN's flow control stream filter to allow loading flow control rules from an external configuration center, specifically Nacos. Update the rate-limiting library usage to accommodate its API changes for constructing error objects.

*   Implement the `NacosDataSourceFactoryCreator` function:
    *   Accept an `appName` string parameter.
    *   Return a `DataSourceFactory` implementation and a nil error.
    *   Locate this function in `pkg/filter/stream/flowcontrol/data_source/nacos_data_source_factory.go`.

*   Define the `NacosDataSourceConfig` struct:
    *   Include a `Group` field of type string.
    *   Embed `NacosClientParam` from the `nacos-sdk-go vo` package, which includes `ClientConfig` and `ServerConfigs` fields.
    *   Locate this struct in `pkg/filter/stream/flowcontrol/data_source/nacos_data_source_factory.go`.

*   Update the `DataSourceFactory` interface:
    *   Ensure the `CreateDataSource` method accepts a `config` argument of type `interface{}`.
    *   Marshal/unmarshal the `config` into a `NacosDataSourceConfig`.
    *   Return a `*NacosDataSource` (typed as a `DataSource` interface) and a nil error for valid configurations.
    *   Locate this interface in `pkg/filter/stream/flowcontrol/data_source/data_source_factory.go`.

*   Implement the `DataSource` interface:
    *   Expose the `InitFlowRules() error` method.
    *   Ensure the `NacosDataSource` type implements this method and is the concrete type returned by `CreateDataSource`.
    *   Locate this interface in `pkg/filter/stream/flowcontrol/data_source/data_source_factory.go`.

*   Update the sentinel rate-limiting library usage:
    *   Use the options-function wrapper pattern for block error construction.
    *   Pass the block type via a `WithBlockType` option instead of as a positional argument.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.