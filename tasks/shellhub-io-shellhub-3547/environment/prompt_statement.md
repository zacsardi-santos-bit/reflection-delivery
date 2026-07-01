I'm working on the device listing logic in our server and I've noticed a few issues with how device acceptability is determined when listing devices for a namespace.

First, in our community self-hosted mode, when we list pending devices, we're making an unnecessary database call to count previously removed devices — that information isn't used in community mode and shouldn't be needed there at all. That call belongs only in the cloud-hosted path.

Second, and more importantly, when a namespace still has room to accept more devices, the devices returned in the listing don't have their "acceptable" flag set to true for non-accepted devices. So even when a namespace hasn't hit its limit, operators can't tell which pending devices they can actually accept. The flag is just always false, which is wrong.

I think we need to better separate the logic by deployment mode — cloud, enterprise, and community each have different rules for how to determine acceptability based on namespace capacity. Cloud mode needs to check how many devices were historically removed when computing whether the limit is reached. Enterprise and community should each just check whether the current device count is at the limit. When the namespace has capacity (or has no limit at all, indicated by a limit of -1), non-accepted devices should be marked as eligible for acceptance.

There also needs to be an appropriate error returned when the cloud mode fails to retrieve the count of removed devices, so callers can handle that failure correctly.

Could you update the device listing service and the underlying data layer to fix these issues across all three deployment modes?
