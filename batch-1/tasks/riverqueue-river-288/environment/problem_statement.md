## Description

Right now, all recurring scheduled jobs in River must be declared when the client is first set up. Once the client starts running, there is no way to add new scheduled jobs or remove existing ones without restarting the entire process. This is a significant limitation for applications that need to dynamically enable or disable certain background work at runtime — for example, in response to configuration changes, feature flags, or tenant provisioning.

## Expected Behavior

- After a client has already started, developers should be able to add one or more new recurring jobs that immediately join the scheduling loop.
- Newly added recurring jobs that are configured to run immediately upon addition should be enqueued right away.
- Previously added recurring jobs should be removable individually using a reference (handle) returned when the job was added.
- Multiple jobs should be removable in a single call using a list of handles.
- All currently scheduled recurring jobs should be clearable at once with a single call.
- After clearing all jobs, new jobs can be added with fresh handles that are distinct from any previously assigned ones — handles are never reused.
- All these operations should be safe to call concurrently from multiple goroutines.

## Why This Matters

Many production systems need to adjust their background job schedules at runtime. Being forced to restart a client process just to add or remove a scheduled task is operationally expensive and introduces unnecessary downtime. Dynamic management of periodic jobs makes the system much more flexible for real-world use cases.
