I'm working on improving the adaptive operation tracker in our distributed storage system. Right now it tracks latency at the datacenter level or per partition, but I need it to also support finer-grained tracking at the individual data node level and the individual disk level.

The idea is: when we send a read request to a replica, we compare how long it's been inflight against a historical latency distribution for that resource. If it's past the threshold, we issue a speculative request to another replica. Currently, all replicas in a datacenter (or all replicas of a partition) share the same latency distribution. I want to be able to configure the system so that each data node has its own distribution, or each disk has its own distribution.

The metrics layer needs to be updated to use a more general "resource" abstraction as the map key, so that partitions, data nodes, and disks can all be used interchangeably. The existing partition-level histogram maps should be generalized to work with any resource type.

The tracker scope configuration needs two new options — one for data node granularity and one for disk granularity. When node-level scope is configured, the histogram maps should be populated with one entry per data node in the cluster, split by whether the node is local or remote. When disk-level scope is configured, the maps should be populated with one entry per unique disk, again split by locality. When datacenter-level scope is used (the existing default), these finer-grained maps should remain unallocated.

The tracker itself needs to select the right histogram when checking whether a request is past due: for node-level scope, look up by the replica's data node; for disk-level scope, look up by the replica's disk.
