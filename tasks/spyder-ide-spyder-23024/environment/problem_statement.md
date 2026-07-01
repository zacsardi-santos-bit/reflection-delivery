## Description

The IDE's configuration system does not validate the format of shortcut key names when plugins provide their default shortcut configurations. Shortcut keys are expected to follow a strict two-part structure: a context identifier, a separator, and a shortcut name. However, if a plugin developer accidentally provides a key with no separator at all, or a key with too many separators, the invalid configuration is accepted silently and only causes confusing, hard-to-trace errors later during shortcut processing.

## Expected Behavior

- When a plugin registers shortcut defaults with a key that has no separator between context and name, the system should immediately reject the configuration with a clear error.
- When a plugin registers shortcut defaults with a key that has more than one separator, the system should also immediately reject the configuration with a clear error.
- In both cases, the error should be raised at configuration initialization time, not deferred until the shortcuts are later looked up or applied.

## Why This Matters

Plugin authors spend significant time debugging mysterious failures caused by misconfigured shortcut keys. Failing fast at configuration load time makes such mistakes immediately obvious and prevents them from silently corrupting the shortcuts system. It also makes the contract for shortcut key names explicit and enforceable.
