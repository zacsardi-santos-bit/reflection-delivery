## Description

When reviewing a potentially malicious package on the package index, administrators currently have two choices: mark it as "not malware" and leave it active, or fully remove the project as malware (an irreversible action). There is no intermediate, reversible option for projects that are suspicious but not yet confirmed as malware.

We need a **quarantine** capability that allows admins to make a project unavailable for installation and prevent owners from modifying it, while keeping the project in the system. This is essential when a project needs further investigation before a final verdict — administrators should be able to quarantine it quickly, then reverse the decision if it turns out to be a false alarm.

## Expected Behavior

- A utility function should be available to place a project into quarantine, recording who initiated the action and when. A corresponding function should remove a project from quarantine.
- Both functions must support an optional flash message parameter controlling whether a session notification is shown.
- Admins should be able to quarantine a project from the malware reports project list view and from the malware report detail view.
- Admins should be able to remove a project from quarantine via the project detail view.
- All three actions should be accessible via dedicated admin routes.
- Flash messages confirming the quarantine or quarantine removal should remind admins to update related support conversations.

## Why This Matters

This feature gives administrators a safe, reversible intermediate action when handling suspicious packages. Rather than immediately destroying a project (and impacting the owner permanently), admins can quarantine it for further review and restore it if appropriate.
