I'm working on the AWS Load Balancer Controller and running into a couple of issues with how security add-ons (WAF Classic, WAFv2, and Shield Advanced protection) are managed on load balancers.

First, there's no way to explicitly disable these security features through ingress annotations. Right now, if I want to remove a WAF ACL association or disable Shield protection, I can only remove the annotation — but that just leaves whatever is already on the load balancer untouched. I'd like to be able to set the annotation to a sentinel value like "none" (or "false" for boolean settings like Shield) to explicitly signal that the feature should be turned off.

Second, when reconciliation of these add-ons fails, the error messages aren't descriptive enough. They don't tell me which security service had the problem, so I have to dig through logs to figure out whether it was a WAFv2 issue, a WAF Classic issue, or a Shield issue.

For Shield specifically, the controller should track an explicit enabled/disabled state — not just treat presence of the annotation as "enabled". And when removing a Shield protection, it should only remove ones that the controller itself created, leaving externally-managed protections alone.
