I'd like to add an option to skip the up-front plugin pre-installation phase that the engine performs before a deployment.

*   The UpdateOptions struct must include a boolean field SkipPluginPreInstall (default false). When false, the engine performs the existing up-front pre-installation check by calling GetPluginPath on the PluginManager for every plugin the language host advertises as required, including packages that are imported but never used at runtime. When true, the engine skips this pre-installation traversal entirely.

*   When SkipPluginPreInstall is false, the engine must call GetPluginPath on the PluginManager for all plugins reported by the language host. For a program that advertises two packages (e.g. pkgA@1.0.0 and pkgB@1.0.0), GetPluginPath must be called for both, regardless of which packages are actually used at runtime.

*   When SkipPluginPreInstall is true, the engine must not call GetPluginPath for any plugins during the pre-installation phase. The set of plugins considered for up-front installation must be empty.

*   In both modes (SkipPluginPreInstall true or false), providers that are actually needed at runtime must still be loaded lazily by the provider registry when resources of that type are registered. A provider for a package that registers a resource must still be loaded exactly once; a provider for a package whose resources are never registered must not be loaded.

*   The TestUpdateOptions struct in the lifecycle test framework must expose a PluginManager field of type engine.PluginManager. When this field is non-nil, it must be used as the engine.Context.PluginManager for the test run. When nil, the engine context must default to using NopPluginManager{}.


*   Interface details: Type: Struct Field
Name: SkipPluginPreInstall
Location: pkg/engine/update.go
In Struct: UpdateOptions
Field Type: bool
Description: When true, the engine skips the up-front plugin pre-installation traversal. Missing plugins are loaded lazily by the provider registry when first needed. When false (default), the engine calls GetPluginPath on the PluginManager for every plugin reported by the language host before deployment begins.

Type: Struct Field
Name: PluginManager
Location: pkg/engine/lifecycletest/framework/framework.go
In Struct: TestUpdateOptions
Field Type: engine.PluginManager
Description: When non-nil, this value is used as the engine.Context.PluginManager for the test run, allowing tests to observe or intercept plugin installation calls. When nil, defaults to NopPluginManager{}.

Type: Interface
Name: PluginManager
Location: pkg/engine/ (e.g., pkg/engine/plugins.go or pkg/engine/context.go)
Description: Interface representing the plugin management system consulted during pre-installation. Must include the method GetPluginPath(ctx context.Context, sink diag.Sink, plug workspace.PluginDescriptor, projectPlugins []workspace.ProjectPlugin) (string, error).

Type: Struct
Name: NopPluginManager
Location: pkg/engine/lifecycletest/framework/
Description: Exported struct that implements engine.PluginManager with no-op methods. Used as the default PluginManager in the engine context for test runs. Must be embeddable so test-specific plugin managers can extend it.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.