## Description

Right now, assigning roles to users only works from the user's perspective — you call a method on a user (or other model) to give it a role. There is no convenient way to do the inverse: take a role and say "assign this role to all of these users at once," "remove this role from these users," or "make sure exactly these users have this role."

This is a real pain point when building admin screens or background jobs that manage role membership at the role level. For example, if you're building a page that shows all users with a given role and lets an admin add or remove members, you have to loop through each user individually and call methods on each one.

## Expected Behavior

- It should be possible to call a method on a role to assign that role to multiple models at once, without affecting any existing assignments.
- It should be possible to call a method on a role to remove it from a list of models, without affecting other models that have the role.
- It should be possible to synchronize the set of models that have a role — providing a new set should cause previous models not in that set to lose the role, while the given models gain it.
- All three operations should accept model instances, raw IDs, or a mix; a single model or ID should also be accepted (not just arrays).
- When passing raw IDs, there should be a way to specify which model class they belong to — either per-call or via a package configuration option.
- All three operations should be safe against duplicates: passing the same model or ID more than once should never create duplicate database records.

## Why This Matters

This makes role management much more natural when you are thinking at the role level rather than the user level, and eliminates boilerplate loops in common admin and batch-processing scenarios.
