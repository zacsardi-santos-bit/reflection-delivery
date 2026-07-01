I'm working on hardening the security context of containers injected by the Consul Kubernetes service mesh webhook. Right now, the sidecar proxy container that gets injected into application pods does not explicitly prevent privilege escalation, even though it already sets a non-root user and group and uses a read-only root filesystem. I'd like the injected sidecar to also explicitly disable privilege escalation so that no process inside it can gain additional privileges at runtime.

Similarly, the init container that sets up network traffic redirection when CNI is active is missing both the read-only root filesystem setting and the privilege escalation prevention — even though it already drops all Linux capabilities. I'd like both of those restrictions added to its security context as well.

Both the standard webhook path and the v2 webhook path should have these security hardening settings applied consistently.
