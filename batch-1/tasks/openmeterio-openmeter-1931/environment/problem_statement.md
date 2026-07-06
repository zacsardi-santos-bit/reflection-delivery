## Description

The billing system can validate invoices against connected external apps, but has no mechanism to actually synchronize, finalize, or delete invoices through those apps. The invoicing lifecycle is currently disconnected from third-party providers: invoices are processed internally, but the external system is never notified.

This means:
- External apps cannot assign identifiers to invoices or individual line items during sync
- No payment reference can be recorded when an invoice is finalized
- Invoice deletion failures are not tracked or surfaced properly
- Retrying a failed invoice still fails because critical errors from the previous attempt block the retry

## Expected Behavior

- When an invoice is synced to draft status, the connected invoicing app should be called and allowed to assign external identifiers to the invoice and to individual fee-type line items
- When an invoice is finalized, the app should be called to both sync and finalize the invoice, and the resulting payment reference should be stored on the invoice
- When an invoice is deleted, the connected app should be notified; if the deletion fails with a validation error, the invoice should be soft-deleted with a "delete failed" status and the error returned to the caller
- When a generic (non-validation) error occurs during deletion, retrying should surface the error as a validation issue on the invoice
- When retrying a failed invoice, the system should demote any existing critical validation issues before reattempting, so prior errors do not block the new attempt
- The component identifier attached to validation issues should follow a standardized format that includes the specific operation being performed (validate, sync, delete, finalize)

## Why This Matters

Without these integrations, the billing service cannot fully participate in the invoice lifecycle with external systems. Operators cannot track which invoices or line items have been synchronized, cannot see payment references, and cannot recover gracefully from failed deletions.
