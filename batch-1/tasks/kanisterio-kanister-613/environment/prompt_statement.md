I'm working on improving the zone selection logic used when restoring or cloning block storage volumes across cloud regions. Right now the function that resolves which availability zones to use in the target region creates its own Kubernetes client internally. This makes it hard to test in isolation and prevents the caller from handling connectivity failures gracefully before zone resolution is attempted.

I'd like to refactor this so the Kubernetes client is passed in as a parameter instead of being created internally. The storage provider (AWS EBS, GCP, Azure, etc.) should create the client and pass it in, and can log or ignore client creation failures before calling the zone resolution function.

Along with this, I'd like to make the helper that filters a set of candidate zones against a list of valid zone names into a public function, so that storage provider packages can call it directly without going through the higher-level resolution logic. Currently it's private, which forces providers to either duplicate logic or use a function with more complexity than they need.

The zone resolution function should clearly distinguish between two error conditions: when there are no valid zones available for a region at all, and when the region itself isn't recognized by the provider's zone mapping. Each case should produce a distinct error message.
