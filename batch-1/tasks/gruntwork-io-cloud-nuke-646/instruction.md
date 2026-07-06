Implement support for AWS Route 53 resources in the cloud-nuke tool to automate the cleanup of hosted zones, CIDR collections, and traffic policies. Ensure name-based filtering using regular expressions is available for these resources, and update the configuration system to recognize them.

*   Define the `Route53CidrCollection` struct in `aws/resources/route53_cidr_collection_types.go`:
    *   Include fields: `Client` of type `route53iface.Route53API`, `Region` as a string, `Ids` as a slice of strings, and embed `BaseAwsResource`.
*   Implement the `getAll` method in `aws/resources/route53_cidr_collection.go`:
    *   Use `Client.ListCidrCollections` to list CIDR collections.
    *   Filter results using `configObj.Route53CIDRCollection` with regex matching.
    *   Return a slice of matching collection ID values as `[]*string`.
*   Implement the `nukeAll` method in `aws/resources/route53_cidr_collection.go`:
    *   Accept `[]*string` identifiers.
    *   For each ID, list and remove associated CIDR blocks using `ListCidrBlocks` and `ChangeCidrCollection`.
    *   Delete the collection with `DeleteCidrCollection`.
    *   Return `nil` on success.

*   Define the `Route53HostedZone` struct in `aws/resources/route53_hostedzone_types.go`:
    *   Include fields: `Client` of type `route53iface.Route53API`, `Region` as a string, `Ids` as a slice of strings, and embed `BaseAwsResource`.
*   Implement the `getAll` method in `aws/resources/route53_hostedzone.go`:
    *   Use `Client.ListHostedZones` to list hosted zones.
    *   Filter results using `configObj.Route53HostedZone` with regex matching.
    *   Return a slice of matching hosted zone ID values as `[]*string`.
*   Implement the `nukeAll` method in `aws/resources/route53_hostedzone.go`:
    *   Accept `[]*string` identifiers.
    *   Call `DeleteHostedZone` for each ID.
    *   Return `nil` on success.

*   Define the `Route53TrafficPolicy` struct in `aws/resources/route53_traffic_policy_types.go`:
    *   Include fields: `Client` of type `route53iface.Route53API`, `Region` as a string, `Ids` as a slice of strings, and an unexported `versionMap` of type `map[string]*int64` (initialize with `make(map[string]*int64)`).
    *   Embed `BaseAwsResource`.
*   Implement the `getAll` method in `aws/resources/route53_traffic_policy.go`:
    *   Use `Client.ListTrafficPolicies` to list traffic policies.
    *   Filter results using `configObj.Route53TrafficPolicy` with regex matching.
    *   Return matching ID values as `[]*string`.
    *   Populate `versionMap` with `LatestVersion` for each policy.
*   Implement the `nukeAll` method in `aws/resources/route53_traffic_policy.go`:
    *   Accept `[]*string` identifiers.
    *   Use `DeleteTrafficPolicy` with the version from `versionMap` for each ID.
    *   Return `nil` on success.

*   Update the `config.Config` struct in `config/config.go`:
    *   Add fields: `Route53HostedZone`, `Route53CIDRCollection`, and `Route53TrafficPolicy` as `ResourceType`.
    *   Ensure fields are appended after the existing `VPC` field.
    *   Use YAML tags matching their field names.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.