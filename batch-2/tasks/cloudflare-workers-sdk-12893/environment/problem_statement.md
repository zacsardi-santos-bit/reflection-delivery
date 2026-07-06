I'm cleaning up the container listing command in our CLI and the non-interactive output is rough. Right now when you run the list command in a non-tty it dumps raw data that's basically unreadable and shows nothing about health. I want it to render a clean formatted table instead with columns for the container ID, name, derived state, live instance count, and last modified time.

The state should come from health counters in the API response. If any instances are failing the container is "degraded", if instances are starting or being scheduled (and nothing's failing) it's "provisioning", if there are active instances with no failures it's "active", and if all the counters are zero it's "ready". Degraded wins over everything else, that priority matters.

I also need a flag for machine-readable output. When that's passed I want a JSON array where each entry has the container's ID, name, derived state, instance count, image reference, version, and both the created and updated timestamps.

Also add a per-page pagination option, and if someone passes 0 or a negative number it should fail right away with a clear error message. In non-interactive mode I want a single unpaginated request rather than trying to page through. Oh and when there are no containers, say so clearly instead of showing an empty structure.

The API endpoint for fetching the list changed too, so we need to hit the updated endpoint, and introduce a new type representing each container application (with its health counters) in the shared package. API errors should be descriptive, with distinct messages for bad request (client-side) errors versus server errors.

Same non-interactive table treatment should apply to the command that lists individual container instances, it should show a table rather than JSON by default when non-interactive.
