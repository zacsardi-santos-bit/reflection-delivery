Extend the billing service to integrate fully with external invoicing apps, covering the entire invoice lifecycle. Implement synchronization, finalization, and deletion of invoices through these apps, ensuring proper error handling and identifier management.

*   Update the `AppTypeCapabilityToComponent` function to accept an operation name as a string and format the component name as 'app.{appType}.{capability}.{operation}'.
    *   Use 'validate' as the operation string for the validate step.
*   Extend the `InvoicingApp` interface with the following methods:
    *   `UpsertInvoice(ctx context.Context, invoice Invoice) (*UpsertInvoiceResult, error)`
    *   `FinalizeInvoice(ctx context.Context, invoice Invoice) (*FinalizeInvoiceResult, error)`
    *   `DeleteInvoice(ctx context.Context, invoice Invoice) error`
*   Implement `UpsertInvoiceResult` as a type alias for `UpsertResults` with:
    *   Methods: `AddLineExternalID(lineID, externalID string)`, `SetInvoiceNumber(string)`, `GetInvoiceNumber() (string, bool)`, `GetLineExternalID(lineID string) (string, bool)`, `GetLineExternalIDs() map[string]string`
    *   Constructor: `NewUpsertInvoiceResult()`
*   Implement `FinalizeInvoiceResult` with:
    *   Methods: `SetPaymentExternalID(string)`, `GetPaymentExternalID() (string, bool)`
    *   Constructor: `NewFinalizeInvoiceResult()`
*   Modify the `Invoice` entity:
    *   Add `ExternalIDs` field of type `InvoiceExternalIDs` with fields `Invoicing` and `Payment`.
    *   Implement `FlattenLinesByID()` returning a map of lines keyed by ID.
*   Modify each invoice `Line` (`LineBase`):
    *   Add `ExternalIDs` field of type `LineExternalIDs` with field `Invoicing`.
*   Implement synchronization and finalization logic:
    *   Call `UpsertInvoice` during draft sync, applying returned IDs to the invoice.
    *   Call `UpsertInvoice` and `FinalizeInvoice` during finalization, storing the payment ID.
*   Implement deletion logic:
    *   On `DeleteInvoice` validation error, soft-delete the invoice, set status to `InvoiceStatusDeleteFailed`, and return the error.
    *   On generic error, return invoice with `InvoiceStatusDeleteFailed` and a critical validation issue.
    *   On successful retry, return invoice with `InvoiceStatusDeleted` and no validation issues.
*   Implement retry logic:
    *   Downgrade existing critical validation issues to Warning before retrying.
*   Extend `MockApp` in the sandbox package:
    *   Add methods: `OnDeleteInvoice(err error)`, `OnUpsertInvoice(cb InvoiceUpsertCallback)`, `OnFinalizeInvoice(result *billingentity.FinalizeInvoiceResult)`
    *   Ensure `DeleteInvoice`, `UpsertInvoice`, and `FinalizeInvoice` call registered callbacks.
    *   Implement `Reset()` to clear mock state and `AssertExpectations()` to verify all mock methods were called.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.