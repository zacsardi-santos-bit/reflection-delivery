I'm working on the OpenTelemetry Collector and need to update how telemetry resource attributes are configured.

*   The resource configuration type must support two formats: a legacy inline key-value format and a new declarative format using a typed attributes list. Both formats must be parseable from configuration without error, and the correct format must be auto-detected during unmarshaling.

*   In the legacy inline format, string values are treated as attribute overrides and nil values cause the corresponding default attribute to be removed from the resource; nil-valued legacy keys must NOT be stored in the LegacyAttributes map.

*   In the declarative format, a nil attribute value must be stored as the string '<nil>'; bool values must be stored as bool; signed integer types (int, int8, int16, int32, int64) must be stored as int64; uint8, uint16, uint32 must be stored as int64; uint and uint64 must be stored as their decimal string representation (e.g. uint(200) becomes '200'); float32 and float64 must be stored as float64; any other unsupported type must be stored as its default Go string representation (e.g. complex(1,2) becomes '(1+2i)').

*   The resource configuration struct must include a SchemaUrl *string field (optional), a Detectors field for forward compatibility, and a LegacyAttributes map[string]any field for the inline legacy format. The Attributes list (from the embedded config.Resource) and the SchemaUrl and Detectors fields must be marshaled back correctly when the config is serialized.

*   Validation of the resource configuration must return an error containing 'resource::attributes_list is not currently supported' when the attributes_list key is set.

*   Validation must return an error containing 'resource::attributes cannot be used together with legacy inline resource attributes' when both the declarative Attributes list and LegacyAttributes are non-empty simultaneously.

*   Validation must return an error when a legacy attribute value is a nested map (complex object type).

*   The Config.Resource field must be of type migration.ResourceConfigV030 (the new wrapper type in the internal migration package), replacing the previous map[string]*string type.

*   The telemetry.Settings struct must include a Resource *pcommon.Resource field; when this field is nil, createLogger, createMeterProvider, and createTracerProvider must each return a sentinel error (errMissingCollectorResource) and nil for the created object.

*   When the legacy inline resource attribute format is in use (LegacyAttributes is non-empty), createLogger must emit a warning-level log entry with a message containing 'legacy service.telemetry.resource inline map format'.

*   The defaultAttributeValues package-level function variable must accept a component.BuildInfo and return a map containing 'service.name' (set to BuildInfo.Command), 'service.version' (set to BuildInfo.Version), and 'service.instance.id' (set to a randomly generated UUID string); if it returns an error, createResource and createInitialResourceConfig must propagate that error.

*   createInitialResourceConfig must combine the default attribute values with the resource configuration: it must set SchemaUrl, populate default attributes, and exclude any attribute names that were set to nil in the legacy format. It must return an error if defaultAttributeValues fails.

*   createFixedResourceConfig must convert an existing pcommon.Resource into a config.Resource, copying SchemaUrl from the ResourceConfigV030 config and converting all resource attributes to config.AttributeNameValue entries. It must return errMissingCollectorResource when the pcommon.Resource pointer is nil.

*   The Factory's CreateResource method must create an independent pcommon.Resource for each call; it must not cache or share resources across invocations with different configurations.

*   The New function and its associated tests in the service/internal/resource package must be removed; resource creation is handled exclusively through the new resource configuration infrastructure in the otelconftelemetry package.


*   Interface details: Type: Struct
Name: ResourceConfigV030
Location: service/telemetry/otelconftelemetry/internal/migration/v0.3.0.go (or similar file in that directory)
Description: Migration wrapper around config.Resource (from go.opentelemetry.io/contrib/otelconf/v0.3.0) that adds support for the legacy inline resource attribute format. Embeds config.Resource using mapstructure squash (so fields Attributes []config.AttributeNameValue, SchemaUrl *string, Detectors *config.Detectors are promoted). Adds LegacyAttributes map[string]any using mapstructure remain (captures unknown keys).
Signature:
  type ResourceConfigV030 struct {
      config.Resource   `mapstructure:",squash"`   // embedded; promotes Attributes, SchemaUrl, Detectors, AttributesList
      LegacyAttributes map[string]any `mapstructure:",remain"`
  }
  func (r *ResourceConfigV030) Unmarshal(conf *confmap.Conf) error
  func (r *ResourceConfigV030) Validate() error

Notes on Unmarshal behavior:
- mapstructure ",squash" causes all known config.Resource keys (attributes, schema_url, detectors, attributes_list) to be captured into the embedded struct.
- mapstructure ",remain" causes all unknown keys to be captured into LegacyAttributes.
- If a key is nil in the source map, it is NOT stored in LegacyAttributes (nil values are skipped).
- Returns error if the confmap structure cannot be unmarshaled (e.g., "attributes_list" value is an integer instead of a list).

Notes on Validate behavior:
- Returns error containing "resource::attributes_list is not currently supported" if the AttributesList field (from embedded config.Resource) is non-nil.
- Returns error containing "resource::attributes cannot be used together with legacy inline resource attributes" if both Attributes (non-empty) and LegacyAttributes (non-empty) are present simultaneously.
- Returns error if any value in LegacyAttributes is neither nil nor a string (e.g., a nested map).

---

Type: Variable
Name: errMissingCollectorResource
Location: service/telemetry/otelconftelemetry/ (package-level unexported sentinel error)
Description: Sentinel error returned by createLogger, createMeterProvider, and createTracerProvider when the provided settings contain no resource (Settings.Resource is nil).
Signature: var errMissingCollectorResource = errors.New("...") // exact message not mandated by tests, must be a distinct sentinel

---

Type: Variable
Name: defaultAttributeValues
Location: service/telemetry/otelconftelemetry/ (package-level function variable, replaceable in tests)
Description: Function variable (var, not func) that returns the default resource attribute map for a given BuildInfo, including service.name, service.version, and service.instance.id (a randomly generated UUID). Must be a var so tests can replace it.
Signature: var defaultAttributeValues func(component.BuildInfo) (map[string]string, error)

---

Type: Function
Name: createInitialResourceConfig
Location: service/telemetry/otelconftelemetry/resource.go (or similar)
Description: Creates an otelconf *config.Resource from build info and a ResourceConfigV030. Populates default attributes from defaultAttributeValues. Excludes attributes that were set (as keys) in the legacy format — even nil-valued ones. Appends non-nil legacy attributes and declarative attributes. Preserves SchemaUrl from the config. Returns error if defaultAttributeValues fails.
Signature: func createInitialResourceConfig(buildInfo component.BuildInfo, cfg *migration.ResourceConfigV030) (*config.Resource, error)
  where config is "go.opentelemetry.io/contrib/otelconf/v0.3.0"

---

Type: Function
Name: createFixedResourceConfig
Location: service/telemetry/otelconftelemetry/resource.go (or similar)
Description: Converts an already-resolved pcommon.Resource into a *config.Resource suitable for otelconf, copying all attributes and carrying over SchemaUrl from the ResourceConfigV030 config. Returns errMissingCollectorResource when the pcommon.Resource pointer is nil.
Signature: func createFixedResourceConfig(cfg *migration.ResourceConfigV030, res *pcommon.Resource) (*config.Resource, error)
  where config is "go.opentelemetry.io/contrib/otelconf/v0.3.0"

---

Type: Method
Name: CreateResource
Location: service/telemetry/otelconftelemetry/ (method on the Factory type returned by NewFactory())
Description: Creates a pcommon.Resource from the given settings and Config. Must NOT cache resources across different invocations — each call with a different Config must produce an independent resource with its own attribute values.
Signature: func (f *Factory) CreateResource(ctx context.Context, settings telemetry.Settings, cfg *Config) (pcommon.Resource, error)

---

Type: Field
Name: Resource
Location: service/telemetry/otelconftelemetry/config.go (Config struct)
Description: The Resource field on the Config struct changes type from map[string]*string to migration.ResourceConfigV030 (exposed via type alias ResourceConfig = migration.ResourceConfigV030).
Signature: Resource ResourceConfig  // field on Config struct; ResourceConfig = migration.ResourceConfigV030

---

Type: Function (behavior contract)
Name: createLogger
Location: service/telemetry/otelconftelemetry/logger.go (or similar)
Description: Must return errMissingCollectorResource (and nil shutdown) when LoggerSettings.Settings.Resource is nil. When LegacyAttributes are non-empty on cfg.Resource, must emit a warning-level log entry whose message contains "legacy service.telemetry.resource inline map format".

---

Type: Function (behavior contract)
Name: createMeterProvider
Location: service/telemetry/otelconftelemetry/metrics.go (or similar)
Description: Must return errMissingCollectorResource (and nil meter provider) when MeterSettings.Settings.Resource is nil.

---

Type: Function (behavior contract)
Name: createTracerProvider
Location: service/telemetry/otelconftelemetry/tracer.go (or similar)
Description: Must return errMissingCollectorResource (and nil tracer provider) when TracerSettings.Settings.Resource is nil.

---

Notes on createResource typed attribute conversion (observable behavior via otelconf SDK):
- bool → bool
- int, int8, int16, int32, int64 → int64
- uint8, uint16, uint32 → int64
- uint, uint64 → decimal string (e.g. uint(200) → "200")
- float32, float64 → float64
- nil value in declarative Attributes list → string "<nil>"
- Any unsupported type (e.g. complex) → fmt.Sprint(value) string representation (e.g. complex(1,2) → "(1+2i)")

Notes on service/internal/resource package:
- The config_test.go file in service/internal/resource/ is deleted; the New(buildInfo, map[string]*string) function and its tests are removed from this package. The other tests in that package (detector tests) must continue to pass.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.