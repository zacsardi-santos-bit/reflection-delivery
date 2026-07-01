I'm working on a resource management issue in our distributed job execution system. When a job is cancelled or finishes before its job master ever connects to a worker node, the slots that were pre-allocated on those workers stay in a limbo state — they're marked as allocated but were never actually activated. These ghost allocations don't get cleaned up, which blocks capacity for other jobs.

I need the slot manager to reclaim these inactive slots automatically when a job's resource requirements drop to zero. Specifically, when the requirements are fully cleared, the slot manager should find all worker nodes that hold allocated-but-inactive slots for that job and tell them to release those slots. If the requirements are simply reduced but not zeroed out, no reclamation should happen.

On the worker side, there needs to be a way to handle a request to release all inactive slots for a particular job. When such a request arrives, the worker should free those slots and report them as available back to the resource manager, including their correct slot and allocation identifiers.

There's also a related issue: when a job manager disconnects from the resource manager, the current API doesn't include the job's final status in that notification. I'd like that status to be passed along so other components can make better decisions during cleanup.

Finally, the slot tracker should expose a way to query which worker nodes currently have slots in an allocated (pending or complete) state for a given job, so the slot manager can determine who to notify during reclamation.
