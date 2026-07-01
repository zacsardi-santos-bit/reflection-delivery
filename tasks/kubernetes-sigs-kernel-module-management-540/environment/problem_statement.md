## Description

The kernel module management system currently cannot pull module container images from private container registries because it has no support for image pull secrets. Worker pods that load or unload kernel modules need to authenticate against private registries, but the system doesn't pass credentials into the pods or use them during image pulls.

Additionally, there is some structural redundancy in the module configuration API: the fields identifying a module's name, namespace, service account, and image repository secret are duplicated across both the specification and status types rather than being consolidated into a shared structure.

## Expected Behavior

- A new shared identity structure should group the module name, namespace, service account name, and image repository secret reference together. Both the module specification and module status types should embed this shared structure inline instead of repeating those fields directly.
- When creating loader or unloader worker pods, the system should retrieve the image pull secrets configured on the module's service account as well as any explicitly specified image repository secret, and mount all of them as read-only volumes into the pod.
- The worker should be able to read Kubernetes-style pull secret files from a local directory and use them to authenticate against private container registries when pulling images.
- The image pulling component should accept an authentication keychain so that credentials can be provided when pulling from authenticated registries.
- Configuration helper methods that don't require a request context should have that unnecessary parameter removed from their signatures.

## Why This Matters

Without this capability, kernel module management is limited to public container image registries. Most production environments store their kernel module images in private registries. This change enables the system to work with private registries by properly propagating authentication credentials from Kubernetes secrets into the worker processes that pull and load module images.
