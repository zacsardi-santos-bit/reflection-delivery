I'm working on refactoring the CRI image service in containerd to decouple it from the general plugin configuration. Right now, the image service is impossible to use or test independently because its configuration is embedded in a large general-purpose config structure that also includes runtime, networking, and other unrelated settings.

I need to introduce a separate configuration structure specifically for image-related settings — things like the snapshotter to use, registry mirrors and authentication, pull timeout, and which images are pinned. The existing concept of a single "sandbox image" string should be replaced with a more flexible named map of pinned images.

The image service constructor should be updated to accept this new image-specific configuration along with a simpler options struct (containing just the content store, image store, filesystem paths, runtime platform info, and client) rather than requiring the full plugin context.

Some related changes are also needed: the gRPC-level image operations should move to a dedicated wrapper type rather than living directly on the core service struct; a utility function for parsing image references should be relocated to a shared utilities package; when a runtime handler is not found during snapshotter resolution, the service should fall back gracefully to the default snapshotter rather than returning an error; and the image service field exposed by the server should be exported so it can be used directly by other parts of the system.

The image-specific configuration should also have its own validation function that checks for invalid combinations — for example, it should reject configurations where registry mirrors and a registry config path are both specified at the same time.
