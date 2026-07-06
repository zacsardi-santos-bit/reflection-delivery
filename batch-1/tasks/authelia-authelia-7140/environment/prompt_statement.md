I'm running Authelia with file-based logging and I'd like it to support log rotation without requiring a service restart. The standard way to do this on Unix is to send a hangup signal to the process — the process should then reopen its log file so that log rotation tools can safely move or compress the old file.

Right now, Authelia doesn't listen for this signal at all when it comes to log files. I'd like a signal-listening service to be introduced that, when a log file path is configured, watches for the hangup signal and reopens the log file. If no log file path is configured, no such service should be started. The service should be identifiable by the name "log-reload" and should be categorized as a "signal" type service.

I'd also like the error message when the log file can't be opened to be more descriptive — right now it just shows the raw OS error, which makes it hard to tell what went wrong. It should clearly say that there was a problem opening the log file.

These two things together — the runtime log reload capability and the clearer error message — would make operating Authelia in production much smoother.
