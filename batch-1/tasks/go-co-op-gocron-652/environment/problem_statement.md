## Description

When retrieving all scheduled jobs from the scheduler, the returned list does not preserve a consistent order between calls. Because jobs are stored in a data structure with non-deterministic iteration order, each call to retrieve all jobs may return them in a different sequence.

## Expected Behavior

- Calling the method to retrieve all scheduled jobs multiple times in a row (without adding, removing, or modifying jobs in between) should always return them in the same order.
- The ordering should be stable and deterministic so that two returned slices can be reliably compared for equality.

## Why This Matters

This inconsistency makes it impossible to depend on job list comparisons or any logic that relies on a predictable ordering of jobs. For example, if you retrieve all jobs, store the result, and then retrieve them again, you may get a different ordering even though nothing has changed. This breaks any downstream code that expects stable results from repeated calls to retrieve the job list.
