I'm digging into how Salt minions handle return retries and I think the configured retry count just isn't making it down to the transport layer. There's a minion option that controls how many times a minion retries sending return data back to the master, but when I trace the actual channel send calls the value never gets included, so the setting silently does nothing which is pretty misleading for operators who bump it up expecting more (or fewer) attempts.

Specifically in the minion code, the method that sends request data back synchronously calls the channel's send operation without forwarding the retry count from minion opts as the number of allowed attempts, and the async coroutine-based variant that does the same request send also leaves it out. So I want both of those to pass the configured retry count through so the transport actually honors it.

There's also the mine data send path which is worse off, it's not passing a timeout or a retry count to the channel at all right now, so I want that one fixed too to include both the timeout and the retry count on its send call.

Basically fix the minion's request-send methods (sync and async) plus the mine-send method so the retry count (and the timeout where it's currently missing) get forwarded to the channel's send, and the retry config finally takes effect.
