I'm working on the Hadoop connector configuration system and I'm concerned about sensitive values like passwords being accidentally exposed in log output. Right now, when you retrieve a password from a configuration property, you get back a plain string — there's nothing in the type system to warn you that it's sensitive, and if it gets passed to a logger, the actual secret will appear in the logs.

I'd like to introduce a dedicated wrapper type for sensitive string values. When a password is retrieved from configuration, it should come back wrapped in this type rather than as a raw string. The wrapper should let you explicitly access the underlying value when you really need it, but the key benefit is that any logging done during the retrieval should automatically show a redacted placeholder instead of the real secret.

Non-sensitive configuration values like string collections don't need this treatment — they should continue to log their actual values. But for passwords, the log output should always show something like a redacted marker, never the real credential.

Existing code that calls the password retrieval method and uses the result directly as a string will need to be updated to go through the wrapper's accessor to get the actual value.
