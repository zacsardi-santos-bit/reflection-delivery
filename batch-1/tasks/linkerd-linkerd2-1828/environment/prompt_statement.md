I'm hacking on the service profile bits of our service mesh and hit a few things I want you to fix together. Big one first: over in the profile converter, when a route's match condition has more than one field set, like both an HTTP method and a URL path regex, we currently drop the whole route on the floor instead of combining them. That's silently killing valid config, which is maddening to debug. What I want is for multiple set fields to fold into a single "all must match" rule so every condition applies together, and same deal for response match conditions that carry multiple fields, they should combine the same way rather than getting ignored.

Second, there's a naming mess around response classification. Right now to mark a response class as a failure you set an "is success" field to false, which is a double negative and super easy to misread. I want that flipped to a direct "is failure" boolean with positive semantics, and the YAML serialization keys need to follow, oh and the list of response classes on a route also needs its field name plus its YAML key renamed to match for consistency.

Third, we identify service profiles today by a short service-plus-namespace name, but they should use the fully-qualified cluster-local DNS name instead, so switch profile naming over to the full DNS form.

Finally, the CLI tooling should grow functions to generate an example service profile template using the new field names. The generated template needs to render a valid profile with an illustrative example route that includes a response class configuration so folks have something correct to copy from.

Net effect: no more silent route dropping, clearer failure semantics, consistent DNS-based naming, and a working template example.
