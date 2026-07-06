I'm deep in the live VM migration subsystem and I keep hitting a cluster of related bugs that I want fixed together, they all touch the migration lifecycle so it makes sense to do them in one pass.

First thing, when a migration fails we record a failure end time but sometimes we clobber the start time that was already set. Don't do that, the start timestamp needs to be preserved through failure handling so we don't lose that timing data.

Second, cleanup for VMs that got created specifically as migration targets on the destination node is wrong right now, we do the same cleanup on both success and failure. On failure I want to keep the annotation that marks the VM as having been a migration target so the rest of the system knows to treat it differently, but on success that marker should be fully removed.

Third, I need a function that figures out the right proxy key to use for a given VM during migration. For decentralized migrations (source and target are separate VM instances) and specifically when a UNIX-socket transport is in play, use the source VM's identity as the key. Every other case uses the local VM's own identity, that includes when there's no migration state at all, when a different transport is being used, or when the source identity info just isn't available.

Fourth, the migration transport type needs to be included when we sync migration status from the source node over to the target node, it's missing from that status info today.

And finally the VM lifecycle controller mishandles migration targets in a transitional state. A migration target VM sitting in the "scheduled" phase with a failed or completed (terminated) pod, or whose migration has already been marked failed, should move into a waiting-for-sync state instead of getting treated like a crashed regular VM. Right now these can wrongly flip to Failed which cascades badly.

Why this matters: these edge cases cause migration targets to incorrectly land in Failed, corrupt migration history by overwriting timestamps, leave stale annotations lying around that mess up future reconciliation, or set up proxy connections with the wrong identity so the migration hangs or fails outright. Getting them right improves the reliability and observability of live migration.
