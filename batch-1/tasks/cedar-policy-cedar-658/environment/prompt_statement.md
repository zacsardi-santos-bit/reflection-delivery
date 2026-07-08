I'm hitting a bug in the Cedar policy schema validator around the human-readable (natural) schema serialization. When I take a schema that has an action with no applies-to specification and convert it to the natural schema format, the output wrongly includes an extra applies-to block even though no such constraint existed in the original schema.

What I'd expect is that a completely unconstrained action (one that carries no restriction on principals or resources) should render cleanly, just the action declaration with its name and a terminating semicolon and nothing else tacked on. Right now the serializer injects an empty or default applies-to clause that was never there, so it's adding content out of thin air.

The real problem this causes is broken round-tripping. If I define a bare, unconstrained action and serialize it to the natural format, I can't faithfully re-read it because the serialized form gained an applies-to block that didn't exist, so any tooling that converts schemas to human-readable and back produces wrong results for this case.

Can you fix the natural schema serializer so actions with no applies-to info are displayed correctly, with no injected applies-to clause, and the conversion still succeeds without error? Basically a bare action should come out as name plus semicolon, clean.
