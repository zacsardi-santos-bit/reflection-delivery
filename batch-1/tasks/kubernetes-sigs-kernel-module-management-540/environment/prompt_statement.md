I'm working on the kernel module management system and I need to add support for pulling module container images from private registries using Kubernetes image pull secrets. Right now, the worker pods that load and unload kernel modules can only pull from public registries, which is a major limitation for production environments.

Here's what I need:

The API types for module specifications and statuses currently have name, namespace, service account, and image repository secret fields scattered directly on each type. These should be consolidated into a single shared inline structure that both the spec and status types embed. This makes the types cleaner and makes it easier to pass identity information around.

For the pod management layer, when creating worker pods for loading or unloading kernel modules, the system needs to look up the service account configured for the module, collect all image pull secrets from that service account plus any explicitly configured image repository secret, and mount each of those secrets as a read-only volume into the pod so the worker process can use them.

On the worker side, I need a new function that reads Kubernetes-format pull secret files from a local directory and builds an authentication keychain from them. It needs to support both the legacy format and the newer JSON-based format. The image puller also needs to be updated to accept an authentication keychain so it can use those credentials when pulling images.

Finally, some helper methods for managing module configurations currently take a context parameter that they don't actually need — those should have the context parameter removed.
