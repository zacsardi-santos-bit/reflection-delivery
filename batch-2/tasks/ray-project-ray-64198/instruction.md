I'm working on improving Ray Data's usage telemetry system and I need two related enhancements to the workload payload it records.

*   IssueDetectorManager must expose a get_detected_issues() method that returns a copy of all (IssueType, operator) pairs that have been reported during execution as a set. Reporting the same (issue_type, operator) pair more than once must not add a duplicate — the set size stays the same.

*   collector must expose a _make_usage_op_id(index: int, name: str) -> str function that returns a stable short string identifier derived from the post-order index and anonymized operator name. For example, _make_usage_op_id(0, 'ReadRange') and _make_usage_op_id(1, 'MapBatches') must each produce distinct, deterministic strings.

*   collector must expose a build_usage_id_map(logical_plan) -> Dict[int, str] function that returns a mapping from id(logical_op) to usage_id for every distinct operator in the plan. When the same logical operator instance appears in multiple branches (e.g. a self-zip), it must receive exactly one usage_id — the number of entries in the map must equal the number of discrete operator instances.

*   collector must expose a physical_op_name_with_id(operator, usage_id_map=None) -> str function. When operator._logical_operators is an empty list, it must return the string 'Unknown'. When _logical_operators is non-empty and no usage_id_map is provided, it must return the anonymized names joined with '->'. When a usage_id_map is provided, each logical op name must be suffixed with '-<usage_id>' before joining, producing a format like 'Op1-id1->Op2-id2->Op3-id3'.

*   collector.record_execution_result must accept an optional detected_issues parameter (a list of (IssueType, operator_name_str) tuples, defaulting to None). When provided, it must serialize the issues as a list of {'issue_type': str, 'operator': str} dicts in the payload under the 'detected_issues' key, using the IssueType enum's string value (IssueType.HANGING -> 'hanging', IssueType.HIGH_MEMORY -> 'high memory'). When omitted or None or empty, 'detected_issues' in the payload must be an empty list [].

*   The workload plan tree recorded in the usage payload must include a 'usage_id' field on every plan node alongside the existing 'op' and 'inputs' fields. The flat 'ops' list must also include a 'usage_id' field on every entry alongside the existing 'name' field.

*   Each entry in the usage payload must contain a top-level 'detected_issues' field. When no issues occurred, its value must be an empty list [].


*   Interface details: Type: Method
Name: get_detected_issues
Location: python/ray/data/_internal/issue_detection/issue_detector_manager.py
Signature: get_detected_issues(self) -> Set[Tuple[IssueType, PhysicalOperator]]
Description: Returns a copy of the set of (IssueType, operator) pairs detected so far. Pairs are deduplicated — adding the same pair twice keeps the set size unchanged.

Type: Function
Name: _make_usage_op_id
Location: python/ray/data/_internal/usage/collector.py
Signature: _make_usage_op_id(index: int, name: str) -> str
Description: Returns a stable short string identifier for a logical operator based on its post-order index and anonymized name. Used as the usage_id assigned to plan nodes and operator list entries.

Type: Function
Name: build_usage_id_map
Location: python/ray/data/_internal/usage/collector.py
Signature: build_usage_id_map(logical_plan: "LogicalPlan") -> Dict[int, str]
Description: Traverses the logical plan in post-order and returns a mapping from id(logical_op) to usage_id string. Each distinct operator instance receives exactly one usage_id even when reachable from multiple plan branches (deduplication for shared-node DAGs). Returns an empty dict when usage collection is disabled.

Type: Function
Name: physical_op_name_with_id
Location: python/ray/data/_internal/usage/collector.py
Signature: physical_op_name_with_id(operator: "PhysicalOperator", usage_id_map: Optional[Dict[int, str]] = None) -> str
Description: Returns a human-readable name for a physical operator. When operator._logical_operators is empty, returns "Unknown". Otherwise joins the anonymized names of all constituent logical operators with "->". When usage_id_map is provided and an operator's id is found in the map, appends "-<usage_id>" to its name before joining, e.g. "ReadParquet-aaaaaaaa->MapBatches-bbbbbbbb->Filter-cccccccc".

Type: Function
Name: record_execution_result
Location: python/ray/data/_internal/usage/collector.py
Signature: record_execution_result(execution_id: str, detected_issues: Optional[List[Tuple["IssueType", str]]] = None) -> None
Description: Updated signature — now accepts an optional detected_issues list of (IssueType, operator_name_str) pairs. Serializes them into the payload as a list of {"issue_type": str, "operator": str} dicts using the IssueType enum's string value. When omitted or None or empty, the "detected_issues" field in the payload is [].

Note on data structures:
- Each plan tree node (_PlanNode) must have a "usage_id" field in addition to "op" and "inputs".
- Each entry in the flat ops list must have a "usage_id" field in addition to "name" (and optional "config").
- Each top-level execution entry must have a "detected_issues" field containing a list of {"issue_type": str, "operator": str} dicts.
- IssueType string values: IssueType.HANGING -> "hanging", IssueType.HIGH_MEMORY -> "high memory".


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.