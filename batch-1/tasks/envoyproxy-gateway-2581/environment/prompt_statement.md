I'm working on the Envoy Gateway project and need help adding support for TCP keepalive settings on upstream connections in the xDS translation layer.

We already support TCP keepalive on the listener side, and the intermediate representation already has a type for TCP keepalive configuration. However, when the translator builds Envoy clusters for upstream connections, it ignores any TCP keepalive settings that may be present on the route. Those settings need to be translated into the upstream connection options on the Envoy cluster — specifically mapping the probe count, idle time, and probe interval fields to the equivalent Envoy cluster configuration.

The implementation should correctly handle the case where a route has all three TCP keepalive parameters configured — probe count, idle time, and probe interval — and produce a cluster configuration where those values are properly reflected in the upstream connection options.
