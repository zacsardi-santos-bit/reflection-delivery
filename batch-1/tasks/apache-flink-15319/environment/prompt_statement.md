I'm chasing a resource leak in our distributed job execution system. When a job gets cancelled or finishes before its job master ever connects to a worker, the slots we pre-allocated on those workers get stuck in limbo, they're marked allocated but were never actually activated, so nothing ever releases them and they block capacity for other jobs. These ghost allocations just pile up.

What I want is for the slot manager to reclaim these inactive slots automatically when a job's resource requirements drop to zero. So when the requirements get fully cleared (set to empty), the slot manager finds every worker node holding allocated-but-inactive slots for that job and tells them to release those slots. Important: if the requirements are just reduced but still non-empty, don't trigger any reclamation, only the fully-cleared case does it.

On the worker side I need a new operation to handle a request to release all inactive slots for a given job. When that request comes in the worker frees those slots and reports them back to the resource manager as available, with their correct slot and allocation identifiers intact.

There's a related gap too. When a job manager disconnects from the resource manager, the current API doesn't carry the job's final status in that notification, and I want that status passed along so the resource manager and other listeners can make smarter cleanup decisions.

Oh and the slot tracker needs a way to query which worker nodes currently hold slots in an allocated state for a given job, covering both the pending-allocation and the fully-allocated (complete) states, so the slot manager knows who to notify during reclamation. Basically that query is what drives the whole release fan-out.

Without this, resources leak every time a job dies before its master connects, which wastes capacity and can starve other jobs from scheduling, and threading the job status through disconnect is what makes the recovery actually informed.
