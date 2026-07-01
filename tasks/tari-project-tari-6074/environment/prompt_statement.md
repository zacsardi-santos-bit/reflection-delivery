I'm working on the wallet's output management system and I've noticed a problem with how outputs are handled after they're added. Right now, as soon as an output is added to the wallet's output manager, it's treated as immediately spendable. But that's not right — outputs should only be available for spending once they've been confirmed on-chain.

I need a way to add an output in a "pending" or unconfirmed state, and then separately confirm it as truly unspent and spendable. This confirmation step should be callable directly on the backend storage — not just through the high-level service interface — so test code and other parts of the system can explicitly control when outputs become spendable.

The backend type needs to be cloneable so a reference can be kept after it's been handed off to the service, and the confirmation method should be accessible on both the backend directly and the database wrapper.

There's also a related balance bug I need to fix: when sending a hash time-locked contract transaction, the value being sent is incorrectly subtracted from the pending incoming balance. It should only subtract the fees, not the sent amount itself.
