Implement a jitter mechanism in the router's control plane polling component to prevent synchronized polling requests. Ensure the component accepts both a poll interval and a maximum jitter duration, handling invalid parameters appropriately.

*   Implement the `NewPoll` function in `router/pkg/controlplane/poll.go`:
    *   Accept two parameters: `interval` and `maxJitter`, both of type `time.Duration`.
    *   Panic if `interval` is zero or negative.
    *   Panic if `maxJitter` is negative.
    *   Return a valid poller when `interval` is positive and `maxJitter` is zero or positive.
*   Implement the `Stop` method for the `Poll` type in `router/pkg/controlplane/poll.go`:
    *   Ensure it returns a nil error upon successful stopping of the poller.
*   Implement the `randomDuration` function in `router/pkg/controlplane/poll.go`:
    *   Return a random duration in the range [0, max] when `max` is greater than zero.
    *   Return exactly 0 when `max` is 0.
    *   Panic if `max` is negative.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.