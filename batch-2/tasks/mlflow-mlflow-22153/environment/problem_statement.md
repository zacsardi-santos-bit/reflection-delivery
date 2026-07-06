## Description

The codebase currently has duplicated logic for resolving and routing requests to AI gateway providers. One copy of the gateway provider setup lives in the judge/adapter layer, and a separate path exists in the discovery utilities. This duplication means that when calling a model through a gateway-style URI without the optional LiteLLM library installed, gateway endpoints are not properly supported in the discovery utilities path.

Additionally, the gateway provider class and the function responsible for resolving provider instances are scattered across modules, making it hard to maintain and extend.

## Expected Behavior

- Gateway provider resolution should be centralized in a single shared utility module so that any component can use the same lookup path.
- Calling a model via a gateway-style URI should work reliably when LiteLLM is not installed, routing through the shared provider resolution code.
- The shared provider resolver should support the "gateway" provider type, using the gateway configuration to build a provider that correctly derives the endpoint URL and forwards any extra headers.
- The gateway provider class should expose headers, endpoint URL, model name, and adapter class, consistent with how other built-in providers are accessed.
- Unsupported provider names should produce a clear error indicating the provider is not supported.

## Why This Matters

Having duplicated provider setup logic in multiple places creates maintenance burden and subtle behavioral differences between code paths. Centralizing this logic ensures consistent behavior, removes the gap where gateway URIs were not handled in the discovery utilities fallback path, and reduces the overall amount of code to maintain.
