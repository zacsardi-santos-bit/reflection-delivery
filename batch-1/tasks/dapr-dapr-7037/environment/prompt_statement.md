I need to remove several legacy backwards-compatibility mechanisms from the Dapr security system that were introduced during the transition to the current SPIFFE-based mTLS model. These are overdue cleanups that were originally scheduled for an earlier planned release cycle.

The first issue is that the sidecar injector currently pre-signs certificates for pods it injects and embeds the certificate chain and private key as environment variables in the container. This was needed for older versions of the runtime, but is no longer required. The injector should stop doing this entirely — no certificate material should be pre-fetched or injected into the sidecar environment.

The second issue is that the identity validator has a special code path allowing the injector to request certificates for arbitrary identities (e.g., in a different namespace or with a different name than what the injector's own pod would normally be authorized for). This "override" privilege was needed so the injector could obtain identities on behalf of pods. Now that the injector no longer pre-fetches certificates, this privilege should be completely removed — the injector should be subject to the same identity validation as every other component. Any attempt by the injector to request an identity outside its own should result in an error, not a success.

The third issue is that the old-style colon-separated identity format (where the request ID was a namespace and service account joined by a colon) is still being accepted in some paths. This format should now be outright rejected with a permission denied error.

Related to this, the validator needs to enforce that app IDs are no more than 64 characters long. Requests with longer app IDs should be rejected with a descriptive error indicating the length limit, and requests with exactly 64-character app IDs should succeed.

Finally, signed workload certificates and issuer certificates currently include DNS Subject Alternative Names that were added for backwards compatibility with clients that matched server identity by DNS name rather than SPIFFE URI. Since all modern clients use URI-based matching, these DNS SANs should be removed from all signed certificates.

The validator interface should also be simplified: the boolean return value that was used to signal "duration override" should be removed from the validator and certificate authority signing interfaces, since the override mechanism itself is being removed. Everything that previously threaded this boolean through the stack should be updated to use a simpler two-value return.
