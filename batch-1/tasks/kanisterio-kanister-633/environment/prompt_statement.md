I'm working on adding support for multiple versions of the Kubernetes VolumeSnapshot API in our backup and restore tooling. Right now, the snapshot code only supports the older alpha API version, but we need to also support the newer beta version since many clusters have migrated to it.

I'd like the system to automatically detect which API version is available on the cluster at runtime and use the appropriate implementation. There should be separate, directly-instantiable implementations for each version so they can also be used explicitly when the version is known ahead of time.

The current factory function that creates a snapshotter always returns an object regardless of whether any snapshot API is actually available — I'd like it to return an error instead when neither version is supported by the cluster.

I also need public helper functions to build the unstructured Kubernetes API objects (snapshot, snapshot content, and snapshot class) for both the alpha and beta versions, since the two versions have different field structures. Similarly, the beta API types (such as snapshot class with a driver field and a deletion policy field, and snapshot content with a deletion policy field) need to be defined in a new dedicated package similar to the existing alpha types package.

Both implementations should support the same full set of operations: creating, getting, deleting, and cloning snapshots, waiting for readiness, retrieving the snapshot source, and looking up snapshot classes by annotation.
