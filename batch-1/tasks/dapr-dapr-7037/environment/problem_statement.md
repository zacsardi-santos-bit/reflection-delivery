## Description

During the transition from an older security model to the current SPIFFE-based identity system, Dapr introduced several backwards-compatibility mechanisms that are no longer needed. These legacy mechanisms now need to be cleaned up:

1. **Pre-signed sidecar certificates**: The sidecar injector currently signs certificates on behalf of pods before injecting them, embedding the certificate material (chain and private key) as environment variables in the injected sidecar container. This is a holdover that is no longer necessary and creates unnecessary security risk.

2. **Injector identity override privileges**: The Kubernetes-based identity validator currently allows the injector to request certificates for arbitrary identities — bypassing normal namespace and identity validation — so it could obtain certificates on behalf of other pods. This privilege must be removed: the injector should be treated like any other component.

3. **Legacy colon-separated identity format**: Older versions of the runtime used a colon-separated namespace and service account identity format. This format was previously accepted as a fallback. It should now be rejected entirely.

4. **DNS Subject Alternative Names in signed certificates**: Workload certificates currently include a DNS SAN derived from the app ID and namespace, and issuer certificates include a cluster-local DNS name for backwards compatibility with older clients. These DNS SANs should be removed since modern clients use SPIFFE URI-based identity matching.

## Expected Behavior

- The sidecar injector must not pre-sign certificates or inject certificate material into pod environment variables.
- The injector must not be able to request certificates for identities other than its own.
- Requests using the legacy colon-separated identity format must be rejected with a permission denied response.
- App IDs longer than 64 characters must be rejected with a clear error message.
- Signed certificates (both workload and issuer) must not contain DNS Subject Alternative Names.

## Why This Matters

These changes reduce the attack surface of the sidecar injector, enforce consistent identity validation across all callers, and remove technical debt from the version-transition period. The codebase has been annotated with TODO comments pointing to these removals.
