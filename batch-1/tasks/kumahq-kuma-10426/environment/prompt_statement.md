I'm working on a multizone service mesh setup where each zone has one or more ingress proxies. I've noticed that when an ingress proxy is temporarily offline and the set of available services changes (for example, a service is removed), the ingress doesn't get updated. When it comes back online, it still advertises the old list of services. This is causing routing failures because other zones rely on the ingress's advertised services to know what's reachable.

The root cause seems to be that the available services information is only updated when an ingress actively requests configuration, so offline ingresses are simply skipped. What I'd like instead is a background component that runs on a regular interval and updates the available services for all ingress proxies in the store, regardless of whether they are currently connected. The interval should be something operators can tune in the configuration.

Additionally, computing which services are available should be a pure, side-effect-free operation that can be called without touching the resource store — so the background component can do the storing separately. Right now there's a function that mixes the computation and the store update together, which makes it harder to reuse.

Can you help introduce this background updater and separate the computation from the store write?
