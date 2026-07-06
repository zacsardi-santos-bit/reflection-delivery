## Description

Salt minions support a configuration option to control how many times they should retry sending return data to the master. However, this configured retry count is never actually forwarded to the underlying transport channel's send operation. The methods responsible for sending request data back to the master — both the synchronous and asynchronous variants — and the method that sends mine data all fail to include the retry count in their channel send calls. As a result, the retry setting has no effect on actual transport behavior.

## Expected Behavior

- When sending a return request synchronously, the retry count from minion options should be passed to the channel's send operation as the number of allowed attempts.
- When sending a return request asynchronously (coroutine-based), the retry count should likewise be included in the channel send call.
- When sending mine data to the master, both a timeout and the retry count should be passed to the channel's send operation. Currently neither is included.

## Why This Matters

Users who configure a custom retry count for minion return attempts expect that setting to actually limit or extend the number of retries the transport layer performs. Without passing this value through, the retry configuration is silently ignored, making the option non-functional and misleading to operators.
