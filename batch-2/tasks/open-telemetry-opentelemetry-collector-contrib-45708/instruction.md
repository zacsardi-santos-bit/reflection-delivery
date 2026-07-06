I'm using the load balancing exporter to distribute metrics across multiple collector backends, and I want to route metrics based on the values of specific attributes rather than by service name or metric name.

*   The Config struct must expose a Validate() error method. When routing_key is set to "attributes" (the value of attrRoutingStr) and routing_attributes is empty, Validate() must return an error with the message: routing_attributes must be specified when routing_key is "attributes" (produced via fmt.Sprintf with %q verb on the attrRoutingStr constant).

*   When routing_attributes is non-empty but routing_key is not "attributes" (including when routing_key is an empty string), Validate() must return an error with the message: routing_attributes can only be used when routing_key is "attributes"; got "<current_key>". Remove routing_attributes or set routing_key to "attributes" (produced via fmt.Sprintf with %q verb substituting attrRoutingStr, the current routing_key value, and attrRoutingStr again).

*   When routing_key is "attributes" and routing_attributes contains at least one entry, Validate() must return nil (valid configuration).

*   A new function splitMetricsByAttributes(md pmetric.Metrics, attrs []string) map[string]pmetric.Metrics must be implemented in the metrics exporter file. It splits the input metrics into separate batches keyed by the concatenated string values of the named attributes, checking resource attributes first, then scope attributes, then datapoint attributes for each remaining key.

*   When all specified attribute keys are found at the resource level, splitMetricsByAttributes must group the entire resource metrics subtree under a single key (no per-datapoint splitting). When some keys remain unfound at the resource level but are all found at the scope level, it must group the entire scope subtree. When keys remain at the datapoint level, individual datapoints must be split.

*   The metrics exporter must accept "attributes" as a valid routing_key value. When this routing key is selected along with routing_attributes, the exporter must route metrics using splitMetricsByAttributes with the configured attribute list.

*   Testdata golden files must be created for the four split_metrics test cases under exporter/loadbalancingexporter/testdata/metrics/split_metrics/{case}/input.yaml and output.yaml, where {case} is one of: basic_attributes, attributes_resource_only, attributes_scope_only, attributes_datapoint_only. Similarly, golden files must be created for consume_metrics attribute routing scenarios under testdata/metrics/consume_metrics/single_endpoint/attributes/ and testdata/metrics/consume_metrics/triple_endpoint/attributes/.


*   Interface details: Type: Method
Name: Validate
Location: exporter/loadbalancingexporter/config.go
Signature: (c *Config) Validate() error
Description: Validates the exporter configuration. Returns an error if the routing key is "attributes" but RoutingAttributes is empty, or if RoutingAttributes is non-empty but the routing key is not "attributes". Returns nil for valid configurations. The routing key string value for attribute-based routing is the package-level constant attrRoutingStr, whose string value is "attributes".

Error message format when routing_key is "attributes" but routing_attributes is empty:
  routing_attributes must be specified when routing_key is "attributes"
  (uses Go %q verb: fmt.Sprintf("routing_attributes must be specified when routing_key is %q", attrRoutingStr))

Error message format when routing_attributes is non-empty but routing_key is not "attributes":
  routing_attributes can only be used when routing_key is "attributes"; got "<current_key>". Remove routing_attributes or set routing_key to "attributes"
  (uses Go %q verb: fmt.Sprintf("routing_attributes can only be used when routing_key is %q; got %q. Remove routing_attributes or set routing_key to %q", attrRoutingStr, c.RoutingKey, attrRoutingStr))
  This applies whether routing_key is a non-attribute key (e.g. "service") or an empty string "".

---

Type: Function
Name: splitMetricsByAttributes
Location: exporter/loadbalancingexporter/metrics_exporter.go
Signature: splitMetricsByAttributes(md pmetric.Metrics, attrs []string) map[string]pmetric.Metrics
Description: Splits a pmetric.Metrics batch into multiple batches keyed by the concatenated string values of the specified attribute keys. Attribute lookup proceeds level-by-level: first resource attributes, then scope attributes, then datapoint attributes. Values found at each level are concatenated (no separator) in the order the attrs slice specifies them. If an attribute key is found at an earlier level (resource), it is included in the key prefix used for all scope/datapoint children; remaining keys are then checked at the next level down.

When all specified attribute keys are found on the resource, the entire resource metrics subtree is grouped under a single key (no per-datapoint splitting needed).
When some attribute keys remain after the resource level, the function proceeds to scope attributes; if all remaining keys are found there, the entire scope metrics subtree is grouped under a single key.
When attribute keys remain after scope, individual datapoints are split by their attribute values.

The returned map uses the concatenated string values (e.g. resource value + scope value + datapoint value) as keys.
Testdata golden files for this function are loaded from:
  exporter/loadbalancingexporter/testdata/metrics/split_metrics/{test_case_name}/input.yaml  (input)
  exporter/loadbalancingexporter/testdata/metrics/split_metrics/{test_case_name}/output.yaml (expected output map)
where test_case_name is one of: basic_attributes, attributes_resource_only, attributes_scope_only, attributes_datapoint_only.

---

Type: Routing key constant
Name: attrRoutingStr
Location: exporter/loadbalancingexporter/ (package-level constant, already defined for traces)
Value: "attributes"
Description: The routing key string for attribute-based routing. Used in Config.RoutingKey to select this routing mode. Already defined in the package for trace routing; the implementation extends it to cover metrics routing as well.

---

Type: Struct field (already exists)
Name: RoutingAttributes
Location: exporter/loadbalancingexporter/config.go (within Config struct)
Signature: RoutingAttributes []string `mapstructure:"routing_attributes"`
Description: Holds the list of attribute key names used to form the composite routing key when routing_key is "attributes". Already present in the Config struct; the Validate() method adds enforcement of its correct use.

---

Note: The newMetricsExporter function (already exists) must be updated to handle attrRoutingStr as a valid routing_key value: when the routing key is "attributes", store the RoutingAttributes from the config and route metrics via splitMetricsByAttributes. Testdata golden files for consume_metrics attribute routing scenarios must also be created at:
  exporter/loadbalancingexporter/testdata/metrics/consume_metrics/single_endpoint/attributes/input.yaml
  exporter/loadbalancingexporter/testdata/metrics/consume_metrics/single_endpoint/attributes/output.yaml
  exporter/loadbalancingexporter/testdata/metrics/consume_metrics/triple_endpoint/attributes/input.yaml
  exporter/loadbalancingexporter/testdata/metrics/consume_metrics/triple_endpoint/attributes/output.yaml


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.