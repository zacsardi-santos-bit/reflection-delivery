I'm working on the nginx agent and I need to add a response flow for configuration upload requests. Right now, when the management plane sends a config upload request, the agent receives it and processes it, but never sends any kind of acknowledgment or status back. The management plane has no way to know if the upload worked or not.

I also noticed that the file management part of the agent doesn't know when a connection to the management plane has been established. It tries to update files or file overviews without checking whether the connection is ready, which causes failures during startup. I'd like the file component to only perform those operations once it has been notified that a connection exists.

Additionally, the messages sent internally between components when a config upload request arrives should carry the full request structure — not just the inner upload portion — so downstream components have all the context they need.

Finally, the config file parser should honor the list of allowed directories specified in the agent configuration when parsing nginx config files, rather than ignoring those restrictions.
