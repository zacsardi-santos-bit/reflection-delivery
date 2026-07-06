I'm running into a problem with how the AWS Load Balancer Controller handles certificate ARNs on an ingress. I have an internet-facing load balancer with multiple TLS certificates specified in the annotation. One of those certificates appears both as the very first entry in the list (which makes it the default certificate) and also appears again later in the list (so it should also be registered as an SNI certificate).

The issue is that the controller is silently deduplicating the certificate list in a way that removes the second occurrence of any certificate that was already seen — including the default one. This means the resulting listener ends up with fewer certificates than I specified: the duplicate entry gets dropped instead of being preserved as an SNI entry alongside the default.

I'd expect the controller to handle this gracefully: the default certificate should not prevent the same ARN from also appearing in the SNI certificate list if it's explicitly listed there. Additionally, any completely empty entries in the comma-separated certificate list (from accidental trailing or double commas) should just be filtered out silently.

Can the certificate merging logic be updated so that when the same certificate is used as both the default and an SNI certificate, both roles are preserved in the final listener configuration?
