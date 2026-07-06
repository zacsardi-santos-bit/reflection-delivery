## Description

When the Central custom resource is deleted, Kubernetes automatically garbage-collects all secrets that have an owner reference pointing to it. This is a problem for the central database password secret — if it gets deleted along with the Central CR, operators cannot re-attach the existing database when recreating the Central instance, leading to potential data loss or broken recovery scenarios.

The current secret reconciliation system only supports one ownership tracking strategy (owner references), which means the database password secret gets deleted whenever the Central CR is deleted. We need a way to mark certain secrets as operator-managed without making them subject to owner-reference cascading deletion.

## Expected Behavior

- The secret reconciliation system should support two ownership strategies:
  - A strategy that sets owner references (existing behavior) — causing secrets to be automatically garbage-collected when the Central CR is deleted
  - A label-only strategy that marks secrets as managed using only a label, without adding an owner reference — allowing secrets to survive Central CR deletion
- The central database password secret should use the label-only strategy, so it persists after the Central CR is deleted and can be reused when the Central CR is recreated
- All other managed secrets (TLS secrets, admin password, scanner database passwords) should continue to use the owner-reference strategy

## Why This Matters

Without this change, re-creating a Central instance after deletion would fail to reuse the existing database because the database password secret is gone. This makes disaster recovery and upgrade workflows unnecessarily fragile.
