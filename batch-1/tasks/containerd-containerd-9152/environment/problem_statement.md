## Description

The CRI image service in containerd is tightly coupled to the full container runtime plugin configuration, making it impossible to instantiate or test the image service independently. Image-specific settings (registry mirrors, authentication, pull timeouts, and the pinned sandbox image) are buried inside a general-purpose plugin configuration structure that also contains runtime, network, and other unrelated settings.

This coupling causes several problems:
- The image service cannot be used or tested without constructing a full plugin config with runtime details
- There is no clear boundary between image-related config and runtime-related config
- A single "sandbox image" string is insufficient for use cases that require tracking multiple named pinned images
- When a requested runtime handler is not found, the snapshotter lookup errors out rather than falling back gracefully to the default snapshotter

## Expected Behavior

- Image-specific configuration should live in its own dedicated structure, separate from the broader plugin config
- It should be possible to validate image configuration independently of the runtime plugin config
- The image service should be constructable directly with just the dependencies it needs (content store, image store, client), without requiring a full plugin context
- The concept of "pinned images" should be generalized to a named map rather than a single sandbox image field
- When a runtime is not found during snapshotter resolution, the service should fall back to the default snapshotter instead of returning an error
- A utility function for parsing image references should be available in a shared utility package
- The gRPC-level image operations should be handled by a dedicated wrapper type, keeping the core image service logic separate from the gRPC transport layer

## Why This Matters

These changes allow the image service to be used and tested in isolation, improve the configurability of pinned images, and make the codebase easier to maintain by establishing clearer boundaries between image management and runtime management concerns.
