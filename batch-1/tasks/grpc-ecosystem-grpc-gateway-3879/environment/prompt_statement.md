I'm working with the grpc-gateway runtime and I've noticed that when I send a JSON request body containing a field set to an empty object — for example, a variant type that selects an option with no sub-fields — the field is completely missing from the generated field mask. This means partial update operations don't pick it up at all, even though the client clearly included it in the request.

For example, if I send a body where one of my fields is set to an empty object value, I'd expect the resulting field mask to list that field. Instead, it seems like empty object values are treated as if the field wasn't present at all, which silently drops the client's intent.

Can this be fixed so that fields with empty object values are still included in the generated field mask?
