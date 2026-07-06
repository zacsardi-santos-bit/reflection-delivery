## Description

PyPI currently has no way for administrators to manually activate an organization's premium membership without requiring the organization to go through the standard payment/billing setup. Some organizations need to be onboarded through special arrangements — partnerships, pilots, or direct agreements — where the normal subscription flow is not appropriate or available.

We need an admin-facing feature that lets platform admins grant an organization a manual activation with:
- A **seat limit** (informational — how many members are allowed under the arrangement)
- An **expiration date** (when the activation should stop being valid)

An organization with an active manual activation should be treated as being in "good standing," the same as an organization with an active subscription. When the activation expires, the organization should lose good standing.

## Expected Behavior

- Admins can add, update, and remove manual activations from the organization admin detail page
- Removing a manual activation requires confirming the organization name to prevent mistakes
- An organization with an active (non-expired) manual activation is treated as operational: members can be invited and premium features are accessible
- An organization with an expired manual activation is treated as inactive
- Seat limits are **informational only** — exceeding the seat limit does not block invitations or revoke good standing
- Community-type organizations continue to be treated as in good standing without any billing or activation required
- Company-type organizations must have either an active subscription or an active manual activation to be in good standing
- When a Company organization is not in good standing, any attempt to invite a new member should be rejected with a clear error message

## Why This Matters

Without this feature, special-case organizations cannot use premium PyPI organization features until they set up billing. This creates friction for partnerships and pilots that are handled outside the standard payment flow.
