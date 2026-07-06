# Bug: Dual-stack CIDR conflict incorrectly releases the valid address family

## Description

In a dual-stack cluster, when a node requests both an IPv4 and an IPv6 pod CIDR and one of those CIDRs turns out to already be in use by another node, the IPAM pod CIDR allocator incorrectly rolls back the allocation for the entire node — including the address family that had no conflict.

This means the legitimately assigned CIDR gets returned to the free pool and may subsequently be handed out to a third node, creating a situation where two different nodes both hold overlapping pod CIDRs.

## Expected Behavior

- When only one address family has a conflict, only that family should be stripped from the node's assignment. The other family's allocation should be preserved.
- The node should be stored with whichever address family was successfully allocated, and an error should be surfaced for the conflicting family.
- The node's spec should be updated to reflect only the successfully allocated CIDRs (the conflicting one omitted), and this spec change must be persisted — not just reported as a status update.
- When both address families conflict, nothing should be stored and no new allocation should be recorded.

## Why This Matters

In a busy dual-stack cluster this bug silently corrupts the CIDR allocation state: a node loses a valid CIDR it should keep, that CIDR lands on another node, and the original node never gets a replacement. The result is routing failures that are difficult to diagnose because the allocator's in-memory state no longer matches what is actually deployed.
