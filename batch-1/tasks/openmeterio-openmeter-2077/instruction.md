Update the billing system to correctly handle subscription item changes mid-cycle, ensuring invoices reflect accurate billing lines and periods. Implement mechanisms to manage finalized invoices and clean up empty draft invoices.

*   Implement logic to update gathering invoices:
    *   If the pro-rated amount is zero, ensure only new item lines appear.
    *   If the pro-rated amount is non-zero, include both pro-rated old item lines and new item lines starting from the change point.

*   Update draft invoices affected by subscription changes:
    *   Adjust line periods and recalculate usage quantities to match the new subscription state.
    *   Delete draft invoices with no remaining lines, setting their status to `billing.InvoiceStatusDeleted`.

*   Handle finalized invoices:
    *   Do not modify finalized invoices. Instead, attach a validation issue with:
        *   Severity: `billing.ValidationIssueSeverityWarning`
        *   Code: `billing.ImmutableInvoiceHandlingNotSupportedErrorCode`
        *   Component: `SubscriptionSyncComponentName`
        *   Path: `lines/{lineID}` where `{lineID}` is the affected line ID.

*   Define constants:
    *   `SubscriptionSyncComponentName` in `openmeter/billing/worker/subscription/sync.go` as `billing.ComponentName = "subscription-sync"`.
    *   `ImmutableInvoiceHandlingNotSupportedErrorCode` in `openmeter/billing/errors.go` as `"immutable_invoice_handling_not_supported"`.

*   Extend interfaces:
    *   Add `UpsertValidationIssues(ctx context.Context, input UpsertValidationIssuesInput) error` to `billing.InvoiceService`.
    *   Add `SnapshotLineQuantity(ctx context.Context, input SnapshotLineQuantityInput) (*Line, error)` to `billing.InvoiceLineService`.

*   Manage gathering invoice line periods:
    *   Update `Period.End` and `InvoiceAt` for lines truncated by new subscription phases.
    *   Delete lines falling entirely after a new phase boundary.

*   Pro-rate in-arrears flat-fee lines upon subscription cancellation:
    *   Set `PerUnitAmount` to the full amount multiplied by the fraction of the billing period used.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.