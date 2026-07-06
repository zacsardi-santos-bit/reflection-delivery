I'm working on the Apache Airflow project and we have a central metrics registry that's supposed to document every metric the codebase emits.

*   The module must export a sentinel constant named _PREFIX_MATCHED that is used as a return value to distinguish a prefix match from an exact/legacy match in find_registry_match.

*   normalize_metric_name must replace every occurrence of a {placeholder} token (curly-brace-delimited identifier) in the metric name string with '*', and return the name unchanged if no such tokens are present. It must handle placeholders at the start, end, or anywhere in the name, including names that also contain underscores.

*   find_registry_match must accept a metric_name string and a registry dict (whose values have 'name', 'type', and 'legacy_name' fields) and return the registry key whose 'name' matches exactly, or whose normalized 'name' matches the normalized input, or whose normalized 'legacy_name' matches the normalized input. For dynamic metric names (those containing {placeholder} tokens) where the normalized name starts with the normalized form of any registry key prefix, the function must return _PREFIX_MATCHED. If no match is found, it must return None.

*   find_registry_match must return None for both static metric names not present in the registry and for dynamic metric names whose normalized prefix does not match any registry entry.

*   get_stats_obj_name must accept an AST expression node and return the node's id attribute for ast.Name nodes, the node's attr attribute (the rightmost attribute name) for ast.Attribute nodes (including deeply nested ones like self.some.module.stats), and None for all other node types.

*   extract_metric_name_from_ast_node must return the string value for static string literal AST nodes, and return None for bare name nodes and function call nodes that cannot be resolved to a string.

*   extract_metric_name_from_ast_node must handle f-string AST nodes: simple variable references become {variable_name}, attribute accesses (e.g. self.dag_file) become {attr_name} using only the last attribute name, and complex inner expressions (e.g. function calls) become {variable}.

*   extract_metric_name_from_ast_node must handle binary string concatenation AST nodes: static string parts are kept as-is, dynamic variable parts are replaced with {variable}, and the parts are concatenated to form the final pattern.

*   scan_file_for_metrics must accept a pathlib.Path, parse the Python source file, and return a list of metric call record objects. Each record must have: metric_name (str), method (str, the method name such as 'incr', 'gauge', 'timing'), stats_obj (str, the stats object name), is_dynamic (bool, True if the metric name contains a placeholder), line_num (int, 1-based line number of the call), and file_path (str, the string representation of the path).

*   scan_file_for_metrics must only recognize calls on objects named 'Stats' or 'stats' (including attribute accesses whose rightmost name is 'Stats' or 'stats'). Calls on any other object name (e.g. 'metrics') must be silently ignored.

*   scan_file_for_metrics must extract the metric name from the first positional argument of the call, or from a keyword argument named 'stat'. Calls where the metric name argument cannot be resolved to a string pattern must be silently skipped (not included in the results).

*   scan_file_for_metrics must return an empty list when the file does not exist and must return an empty list when the file contains a Python syntax error.

*   scan_file_for_metrics must correctly handle files containing multiple stats calls and return one record per resolved call.


*   Interface details: Type: Constant
Name: _PREFIX_MATCHED
Location: scripts/ci/prek/check_metrics_synced_with_the_registry.py
Description: A sentinel value returned by find_registry_match when a dynamic metric name matches a registry entry as a prefix (the metric starts with a known registry key) but is not an exact or legacy match.

Type: Function
Name: normalize_metric_name
Location: scripts/ci/prek/check_metrics_synced_with_the_registry.py
Signature: normalize_metric_name(metric_name: str) -> str
Description: Replaces all {placeholder} style format-string tokens in a metric name with "*". Returns the name unchanged if no placeholders are present.

Type: Function
Name: find_registry_match
Location: scripts/ci/prek/check_metrics_synced_with_the_registry.py
Signature: find_registry_match(metric_name: str, registry: dict) -> str | None
Description: Searches the provided registry dict for a match for the given metric name. Tries exact name match, normalized format-structure match, normalized legacy_name match, and prefix match. Returns the matching registry key name string on success, the _PREFIX_MATCHED sentinel for a prefix-only match, or None if no match is found.

Type: Function
Name: get_stats_obj_name
Location: scripts/ci/prek/check_metrics_synced_with_the_registry.py
Signature: get_stats_obj_name(node: ast.expr) -> str | None
Description: Given an AST expression node, returns the identifier name if the node is an ast.Name node, the attribute name (the rightmost attr) if the node is an ast.Attribute node, or None for all other node types.

Type: Function
Name: extract_metric_name_from_ast_node
Location: scripts/ci/prek/check_metrics_synced_with_the_registry.py
Signature: extract_metric_name_from_ast_node(node: ast.expr) -> str | None
Description: Extracts a canonical metric name pattern from an AST expression node. Handles static string literals (returns the value directly), f-strings (returns a pattern with {variable_name} for simple name references, {attr_name} for attribute access, and {variable} for complex expressions), and binary string concatenation (joins parts with dynamic segments replaced by {variable}). Returns None for bare Name nodes and Call nodes that cannot be resolved to a string pattern.

Type: Function
Name: scan_file_for_metrics
Location: scripts/ci/prek/check_metrics_synced_with_the_registry.py
Signature: scan_file_for_metrics(path: Path) -> list
Description: Parses a Python source file and returns a list of metric call objects for every recognized stats tracking call found. Each returned object has the following attributes: metric_name (str), method (str), stats_obj (str), is_dynamic (bool), line_num (int), file_path (str). Only calls on objects named "Stats" or "stats" (including attribute access ending in those names) are recognized; calls on other objects are ignored. The first positional argument or keyword argument named "stat" is used as the metric name argument. Calls where the metric name cannot be resolved are silently skipped. Returns an empty list for files with syntax errors or files that do not exist.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.