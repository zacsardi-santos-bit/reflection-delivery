I'm working on a game room scheduler system and running into two separate issues.

The first is that when I create a game room spec where a container has no ports defined, validation rejects it even though having no ports is perfectly valid for my use case. Ports should be optional, not required.

The second issue is that when I try to patch a scheduler to change a container's name, the name update doesn't stick — after the patch is applied, the container still has its old name. Other container fields like the image, command, and resource limits are patched correctly, but the name is just silently dropped. I need the container name to be properly updated when it's included in a patch.
