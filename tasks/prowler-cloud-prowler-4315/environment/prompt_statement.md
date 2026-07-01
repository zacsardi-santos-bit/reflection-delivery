I'm working on refactoring the outputs system in a multi-cloud security scanner. Right now, converting a raw check result into a reportable finding involves calling a standalone helper function that manually maps provider-specific fields onto a legacy data model. This is messy — the provider-specific logic (how to display the auth method, which field is the resource name vs. the resource UID, what to use as the region for Kubernetes namespaces, etc.) is hidden inside this helper function and not really part of the finding model itself.

I'd like to introduce a new, unified finding model that can construct itself directly from a provider and a raw check output. The new model should handle all four supported providers (AWS, Azure, GCP, and Kubernetes), each with their own conventions:
- For AWS, the auth method should be prefixed with "profile:", the resource name comes from the resource ID, and the resource UID comes from the ARN
- For Azure, the auth method is the identity type and identity ID joined by a colon
- For GCP, the auth method should be prefixed with "Principal:", and account info comes from the project object
- For Kubernetes, an in-cluster context should display as "in-cluster", the region field should read as "namespace: {name}", and the account name should read as "context: {name}"

I also want to introduce a proper abstract base class for output formats. The base class should automatically call a transform method on instantiation and optionally create a file descriptor. The CSV output should be a concrete implementation of this base class that transforms findings into uppercase-keyed dicts (joining list fields with " | " and formatting compliance dicts as "key: value" entries), then writes semicolon-delimited CSV. The standalone CSV row-writing utility should also be updated to accept either a dict or an object as the row.

Finally, since the new CSV output class manages its own file descriptor, the general-purpose file descriptor initializer should no longer handle CSV mode — it should simply skip it.
