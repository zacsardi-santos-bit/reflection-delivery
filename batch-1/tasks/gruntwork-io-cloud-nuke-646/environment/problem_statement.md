## Description

The cloud-nuke tool does not support Route 53 resources. When cleaning up AWS environments, DNS-related resources — hosted zones, CIDR collections, and traffic policies — are left behind and require separate manual cleanup. This is inconsistent with the tool's goal of providing comprehensive automated AWS resource cleanup.

## Expected Behavior

- The tool should be able to list and delete Route 53 hosted zones.
- The tool should be able to list and delete Route 53 CIDR collections (including removing associated CIDR blocks before deleting the collection).
- The tool should be able to list and delete Route 53 traffic policies.
- All three resource types should support name-based filtering so that specific resources can be included or excluded from deletion using regular expression patterns.
- The configuration system should recognize these three new resource categories, each with its own independently configurable entry.

## Why This Matters

Engineers managing ephemeral AWS environments rely on cloud-nuke to completely clean up all resources. Without Route 53 support, DNS resources persist after everything else is deleted, leading to lingering costs and potential conflicts when re-creating environments. Adding these resource types brings Route 53 cleanup in line with the rest of the tool's supported resources.
