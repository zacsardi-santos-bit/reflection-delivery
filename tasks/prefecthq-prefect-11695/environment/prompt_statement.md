I'm working on making state transitions for flow runs idempotent so that clients can safely retry them. Right now, if a client sends a state transition request and doesn't get a response (due to a network blip), retrying that request causes the server to apply the transition a second time as if it were a brand-new request. There's no way to tell the server "I already sent this one."

I'd like the system to support an optional unique identifier on each state transition. When a client provides this identifier, the server should check whether the current run state already has the same identifier — and if so, reject the incoming transition as a duplicate and return the existing state instead of applying the change again. If either state doesn't have an identifier, or the identifiers differ, the transition should proceed as normal.

On the client side, a fresh unique identifier should be automatically generated and attached to every outgoing state transition request, so callers don't have to manage this themselves.

I also want to make sure that the existing rules around invalid transitions still apply in the non-duplicate case. For example, transitioning a run to the same state type it's already in (when there's no duplicate identifier involved) should still be handled by the existing prevention logic, not silently accepted.
