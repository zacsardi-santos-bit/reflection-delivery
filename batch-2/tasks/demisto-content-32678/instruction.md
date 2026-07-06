Implement a script to identify potentially offending GCP firewall rules based on observed traffic details. Ensure the script evaluates firewall rules against specified criteria and outputs matching rule names. Place the script in the specified directory and ensure all functions are correctly implemented and importable.

*   Implement the `is_port_in_range` function:
    *   Accepts a hyphenated port range string and a port string.
    *   Returns `True` if the port is within the range (inclusive), `False` otherwise.

*   Implement the `is_there_traffic_match` function:
    *   Accepts parameters: `port` (str), `protocol` (str), `rule` (dict), `network_tags` (list).
    *   Returns `True` if:
        *   Rule direction is 'INGRESS'.
        *   '0.0.0.0/0' is in `sourceRanges`.
        *   `disabled` is `False`.
        *   Rule contains an 'allowed' key.
        *   Rule's `IPProtocol` is 'all' or matches the requested protocol.
        *   Rule's port list contains the exact port or a port range including the port.
        *   Rule has no `targetTags` or at least one tag matches the `network_tags`.
    *   Returns `False` if:
        *   Rule's `disabled` field is `True`.
        *   Rule has `targetTags` and none match the `network_tags`.
        *   Requested port and protocol do not match any entry in the rule's `allowed` list.

*   Implement the `gcp_offending_firewall_rule` function:
    *   Accepts a dictionary with keys: `project_id`, `network_url`, `port`, `protocol`, and optionally `network_tags` (default empty list).
    *   Queries GCP firewall rules and identifies matches based on the criteria.
    *   Returns a `CommandResults` object with `readable_output` formatted as: "Potential Offending GCP Firewall Rule(s) Found: {list_of_matching_rule_names}".

*   Ensure the script file is located at `Packs/GCP-Enrichment-Remediation/Scripts/GCPOffendingFirewallRule/GCPOffendingFirewallRule.py`.
*   Ensure all functions (`is_port_in_range`, `is_there_traffic_match`, `gcp_offending_firewall_rule`) are importable from the module named `GCPOffendingFirewallRule`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.