I'm working on adding traffic mirroring support to the HTTPProxy routing configuration. The idea is that when defining a route's services, an operator should be able to mark one of the listed services as a mirror — meaning all incoming traffic will be duplicated and sent to that service in addition to the primary backends, without the mirror's response affecting the client.

I need the routing configuration to recognize this mirror designation on a service and translate it correctly into the underlying proxy configuration so that the mirror traffic policy is actually applied. If more than one service in the same route is marked as a mirror, the route should be treated as invalid and not exposed — only a single mirror per route is supported.

Can you implement this traffic mirroring feature for HTTPProxy routes, including the necessary data model changes and the translation to the proxy's routing layer?
