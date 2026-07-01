I'm trying to harden the security posture of our operator lifecycle manager. I noticed that temporary pods created when unpacking operator bundles don't have any security context configured — they could potentially run as root, acquire elevated privileges, and have no seccomp profile applied. This is inconsistent with production security requirements.

I'd like these bundle unpacking pods to run as a non-root user (specifically user ID 1001, matching the operator registry image), explicitly disallow privilege escalation, drop all Linux capabilities from every container and init container, and apply the runtime-default seccomp profile at the pod level.

Additionally, I noticed that the existing catalog source registry pods are missing an explicit setting to prevent containers from running in privileged mode, even though other security fields are set. That field should be explicitly set to false as well.

Essentially, I want consistent, minimal security settings applied to all pods that the operator lifecycle manager creates, both for unpacking bundles and for serving catalog sources.
