## Description

River currently allows job tags to be any arbitrary string — there are no restrictions on what characters tags can contain or how short they can be. Tags containing commas are silently accepted but break downstream functionality, since commas are used as delimiters in some internal operations. Tags with special characters or very short names can also cause unexpected behavior.

We should add a format constraint for job tags so that invalid values are caught at insertion time with a clear error message.

## Expected Behavior

- Tags must be at least 3 characters long.
- Tags must start and end with a letter, digit, or underscore — not a hyphen.
- Tags may use letters, digits, hyphens, and underscores in the middle.
- Tags must not contain commas or other special characters.

## Why This Matters

Without format enforcement, users can accidentally insert jobs with tags that contain commas — causing silent corruption of tag-related logic — or with tags that are far too short or contain disallowed characters. Catching these mistakes at insertion time gives developers immediate, clear feedback rather than mysterious failures later.
