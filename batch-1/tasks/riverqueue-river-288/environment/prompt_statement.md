I'm working with River's job queue system and I need the ability to dynamically manage recurring scheduled jobs after the client has already started. Currently, all periodic jobs have to be configured upfront, and there's no way to add or remove them at runtime without restarting the process.

What I need is for the client to expose a way to add new recurring jobs on the fly — including immediately enqueuing one when it's first added if that option is set — and to remove previously added jobs using a handle or reference that's returned at add time. I also need to be able to remove multiple jobs at once or clear all of them in one go. All of these operations need to work correctly when called from multiple goroutines simultaneously.

The internal scheduling service should track jobs in a data structure that allows efficient lookup and removal by handle, and the run loop should wake up immediately when new jobs are added so they can be scheduled or inserted without waiting for the next tick. Handles must not be reused even after a full clear of all jobs, so that old references can never accidentally refer to newly added jobs.

The public-facing client API should expose this via a method that returns a bundle object offering add, remove, and clear operations — and this should also work for the initial set of periodic jobs configured at startup.
