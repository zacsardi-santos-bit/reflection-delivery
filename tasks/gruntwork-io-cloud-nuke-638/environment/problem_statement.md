## Description

Cloud-nuke currently has no support for AWS Simple Email Service (SES) resources. When running cloud-nuke to clean up test or sandbox AWS environments, SES resources are silently skipped and left behind, requiring manual cleanup afterward. This is particularly painful for teams that use SES heavily in their test environments and run cloud-nuke regularly to keep costs down.

## Expected Behavior

Cloud-nuke should be able to discover and delete the following categories of SES resources:

- **SES Configuration Sets** — sending configuration profiles used to track email sending events
- **SES Receipt Rule Sets** — rules that route inbound email to various AWS services
- **SES Receipt Filters** — IP address allow/deny filters for inbound email
- **SES Email Templates** — reusable HTML/text templates for sending emails
- **SES Identities** — verified email addresses and domains used for sending

Each resource type should support the existing filtering capabilities already available for other cloud-nuke resource types — specifically, the ability to exclude resources by name pattern and by creation time.

## Why This Matters

Without SES support, teams using cloud-nuke to reset AWS environments must manually identify and delete SES resources. Adding these resource types makes cloud-nuke a more complete solution for environment cleanup, reducing operational overhead and preventing resource leaks in test accounts.
