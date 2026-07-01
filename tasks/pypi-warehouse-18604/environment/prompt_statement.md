I'm working on the PyPI warehouse admin system and need to add support for manually activating organizations without requiring them to go through the normal billing setup. Some organizations are brought on through special arrangements, and right now there's no way to grant them access to premium features without a subscription.

What I need is an admin-only mechanism to grant an organization a manual activation with a configurable seat limit and expiration date. Once an organization has an active manual activation, it should be treated as fully operational — the same way an organization with an active subscription is treated. If the activation expires, the organization should lose that standing and be treated as inactive. Seat limits should be informational: going over them should be visible (so admins and members can see usage), but should not actually block invitations or revoke good standing.

Admins should be able to add, update, and remove these manual activations from the organization admin page. Removing one should require typing the organization name as confirmation. All three operations should record an audit event on the organization and flash an appropriate success or error message.

For the member management side: if a Company-type organization has no active billing and no active manual activation, trying to invite a new member should fail with a clear error message explaining that the organization is not in good standing. This check should not apply to Community-type organizations, which should continue to work without any billing.

The activation form should validate that the seat limit is at least 1, and that the expiration date is strictly in the future. If either field is missing or invalid, a descriptive error should be shown.
