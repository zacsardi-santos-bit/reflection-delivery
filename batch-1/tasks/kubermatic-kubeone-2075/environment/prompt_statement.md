I'm working on our Kubernetes cluster management tool and hit a gap in how we configure hosts. Right now there's no way to cap how many pods land on a given node, so every node just inherits whatever default its runtime hands out. Operators keep asking to tune pod density per host because nodes have different capacities, and we can't tailor resource utilization without it.

What I want is an optional maximum pod count field on the per-host configuration. When it's set, the node should honor that limit, and when it's left unset that's totally fine, the host just falls back to its runtime default like it does today.

The part I really care about is validation. We already have validation logic for host configs, so I just need to extend it to also check this new max pod count. If someone sets it, it has to be a positive number. Zero or a negative value doesn't make sense and should get rejected right there during config validation with a clear error, rather than slipping through and blowing up later during actual node setup. If it's absent, treat the config as valid, no complaints.

So basically: add the optional field, wire it into the existing host config validation path, and make sure positive means ok, zero or negative means error, unset means use the default.
