## Description

When investigating suspicious inbound traffic in a GCP environment, security analysts have no automated way to identify which firewall rules might be permitting that traffic. The GCP enrichment playbook currently lacks the ability to cross-reference observed traffic details (port, protocol, and VM network tags) against GCP firewall rules to surface the potentially offending rules.

## Expected Behavior

- A new automation script should accept a GCP project ID, network URL, port, protocol, and optionally the network tags on the target VM instance.
- The automation should evaluate GCP firewall rules against the supplied traffic criteria and return a list of rule names that could be permitting the observed inbound traffic.
- The evaluation must account for: rules that allow all protocols, exact port matches, port ranges, target tag matching (or absence of target tags), and whether a rule is enabled or disabled.
- Rules that are disabled, have mismatching target tags, or cover different ports/protocols should not be returned as matches.
- When matching rules are found, the output should clearly list the potentially offending rule names.

## Why This Matters

During incident response involving GCP infrastructure, analysts need to quickly narrow down which firewall rules may be exposing a VM to attack. Without this capability, analysts must manually inspect firewall rules — a time-consuming process. This automation integrates into the existing GCP enrichment workflow so that every alert investigation automatically surfaces potentially dangerous firewall rules based on the observed traffic.
