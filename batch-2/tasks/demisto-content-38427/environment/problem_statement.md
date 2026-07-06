I'm updating our Absolute security integration to talk to the provider's v3 API instead of v2, and it touches a bunch of stuff so bear with me. Right now everything hits v2 endpoints with the old request scheme, and v3 changes endpoint paths, payload field names, and response shapes. I want all the freeze message operations (list, create, update, delete) pointed at the updated v3 paths.

The device freeze payload field names need fixing to match the new contract: the scheduled date field and the request name field both got renamed, one now-obsolete field should be dropped entirely, and for device freeze type values I want the case-sensitive strings forwarded exactly as provided, no normalizing.

The device unenroll flow needs a redesign. Instead of just returning a list of devices it should do a multi-step thing: first submit the unenroll request, then fetch a summary of the overall request status including counts of devices in the various states (pending, processing, completed, canceled, failed), and then grab the detailed per-device action records. Combine all of that into a single structured output object.

Also I need a brand new command for removing an existing freeze request from devices, it takes a list of device IDs and returns a readable confirmation message that names the affected device IDs.

Pagination should move to token-based navigation instead of the old offset approach, so when a next-page token is present include it in the query string alongside the page size, and when it's absent just send the page size.

The request preparation method needs to accept a request body param now, oh and an empty body gets signed as-is while a non-empty body should be wrapped in a data envelope before signing. Also the custom device field listing command should pull the device identifier from the command's input arguments, not from the API response, when building its output.

And any commands that used the old event-fetching method should switch over to the new unified request method. Without this, remote freeze/unfreeze/unenroll and event fetching all break for the security teams relying on it.
