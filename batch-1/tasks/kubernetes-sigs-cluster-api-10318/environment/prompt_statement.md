I'm working on the Cluster API project and I'd like to add a safeguard to the cluster topology webhook that prevents operators from initiating a new Kubernetes version upgrade while the cluster is still finishing a previous one.

Right now there's nothing stopping an operator (or an automation tool) from changing the topology version on a cluster whose control plane, machine deployments, or machine pool nodes haven't yet converged to the current version. This can cause overlapping upgrade waves and leave the cluster in a bad state.

I want the webhook that validates cluster topology updates to:
- Check whether the control plane is fully provisioned and at the expected version before allowing a version bump
- Check whether all topology-owned worker groups (both regular worker deployments and machine pools) have finished rolling out the current version before allowing a version bump
- Block the version change with an error if any of those components are still in progress or behind
- Allow an operator to apply a special annotation to their cluster to bypass this check in an emergency — but in that case, return a warning instead of silently allowing it

The shared logic for detecting whether a group of worker machines is mid-upgrade (comparing what version the group is targeting versus what version the individual nodes or machines are actually running) should also be available as a standalone utility function in a shared package, so the same logic can be used from both the controller reconciliation loop and the webhook.
