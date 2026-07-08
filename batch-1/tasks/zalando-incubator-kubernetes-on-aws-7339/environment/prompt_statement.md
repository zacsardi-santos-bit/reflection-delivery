I'm in the middle of bumping our Kubernetes cluster to 1.29 and the config files are lagging behind. Two things I need sorted so nodes come up right and we don't trip over stale flags.

First, the shared cluster defaults config needs machine image (AMI) entries specific to Kubernetes 1.29 added in. Right now there's nothing for that version, so anything trying to provision against 1.29 has no base image to pull. Alongside that, the master node pool stack config has to actually reference those new 1.29 images so the master nodes boot from the correct base. Both files need touching, the defaults where the image entries live, and the master pool stack where they get referenced.

Second thing, there's a feature gate we explicitly turned on a while back for time zone-aware job scheduling (the CronJob timezone stuff). That graduated to stable in 1.29 and it's always-on now, so having the explicit enable entry hanging around in the cluster defaults is pointless and honestly a bit misleading. Worse, Kubernetes can reject unknown or removed feature gate names down the line, so I want that entry gone entirely from the defaults config.

So basically: add 1.29 AMI entries to the defaults, wire them into the master node pool stack, and drop that timezone feature gate line. Keeping everything aligned to the target version is the whole point here, correct images, no leftover flags.
