I'm bumping our Kubernetes cluster to a newer version and I need to fix up our cluster config defaults file to match. Two things here.

First, I need to add image garbage collection thresholds for the node agent (kubelet). Right now we don't have any defaults set for controlling when it starts and stops cleaning up unused container images, which honestly could bite us with unexpected disk behavior. So I want both knobs in there, the high threshold that triggers GC when disk usage climbs past it, and the low threshold where it stops cleaning up. Both need actual default values configured since they're currently missing entirely.

Second thing, there's this feature flag we've been carrying to opt into time zone support for scheduled jobs (CronJobs). In the version we're targeting that's just standard behavior now, always on, and the flag isn't recognized anymore, so I want it removed completely from the defaults. Leaving it around is just gonna cause confusion or weird behavior down the line.

Both edits land in the same cluster configuration defaults file. Basically I want the defaults to stay in sync with what this Kubernetes version actually supports, so the high and low image GC thresholds show up for the node agent and the CronJob time zone flag is gone.
