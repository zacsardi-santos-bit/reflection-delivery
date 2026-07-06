I'm working with the single-threaded executor's turn-based interface and I've run into two related issues.

First, there's no way to tell whether any futures were actually polled during a given turn. The turn method returns a result, but that result carries no useful information — I can't distinguish between a turn that did real work and one that was essentially a no-op. I'd like the return value from each turn to include a flag or method indicating whether any futures were polled, so I can build smarter polling loops and know when the executor is genuinely quiescent.

Second, I've noticed a fairness problem: when one future completes and directly wakes up another future (via an in-memory channel, for example), those directly-notified futures seem to be handled in a separate turn from futures that are woken up by the underlying reactor or park mechanism (e.g., something like a socket becoming readable). This means the executor can end up giving unfair priority to one category of futures over the other. Ideally, both types of ready futures should be processed together within the same turn, so that no category is systematically favored.

Can these two issues be fixed? The polling status should be surfaced on the object returned from each turn, and the scheduler should ensure that directly-woken and reactor-woken futures are both picked up in the same pass.
