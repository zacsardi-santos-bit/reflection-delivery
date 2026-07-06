## Description

The keeper manager component periodically checks whether keeper instances are in the correct replication state and sends correction commands when they appear misaligned. This mechanism works well during normal operation, but it causes problems during datacenter migrations.

During a migration — whether the local datacenter is transitioning to primary or another datacenter is undergoing a migration — the keeper topology intentionally changes as part of the migration workflow. The keeper state checker does not currently understand when a migration is in progress, so it incorrectly treats the transitional keeper topology as an error and sends correction commands that interfere with the migration.

## Expected Behavior

- The keeper state checker should detect when a migration is in progress by comparing the expected keeper master endpoint against the list of known redis instances for the shard and by checking whether the current datacenter is in the primary role.
- When a migration is detected, the checker should still query all keeper instances to observe their current state, but it should refrain from sending any correction commands.
- When no migration is in progress (normal steady-state operation), the checker should continue to send correction commands as before.

## Why This Matters

Sending keeper correction commands during migration can actively disrupt the migration process by reverting intentional topology changes. The keeper manager needs awareness of migration state so that it does not interfere with ongoing migrations, allowing them to complete successfully.
