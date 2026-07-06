I'm updating our Kubernetes cluster to a newer version and need to make some adjustments to our cluster configuration defaults file. Specifically, I need to add configuration parameters for controlling the thresholds at which the node agent begins and stops cleaning up unused container images — there should be both a high threshold (at which garbage collection kicks in) and a low threshold (at which it stops). These settings don't currently exist in our defaults file.

At the same time, there's a configuration flag for enabling time zone support in scheduled jobs that I need to remove entirely from the defaults file, because this is now always enabled in the newer Kubernetes version we're targeting and the flag is no longer needed or recognized.

Both changes should be made in the cluster's configuration defaults file.
