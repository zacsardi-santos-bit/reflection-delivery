I'm working with the distributed key-value store's watch functionality and running into a few issues I'd like to fix.

First, the key-watching feature requires a value to already exist in the store before you start watching — if you call the watch function on a key that hasn't been written yet, it doesn't work correctly. Instead, it should be able to start from an empty state and pick up the first write naturally, without callers needing to pre-populate a dummy value first.

Second, the prefix-watching feature has a couple of bugs. When it reports a key update to the callback, it strips the prefix off the key name before delivering it, so callers only get the suffix. This should be changed so that the full key name is delivered. Also, it sometimes sends duplicate notifications for keys that haven't actually changed — each changed key should be reported exactly once.

Finally, a string codec implementation is currently duplicated across multiple files in the codebase. It would be cleaner to have a single shared implementation in the codec package that everyone can use.
