I'm working on the billing system for subscriptions and running into issues with how invoice lines are updated when a subscription item changes mid-cycle. Right now, when I edit a running subscription — for example, by changing the price of an item — the gathering invoice keeps the old billing lines instead of replacing them with pro-rated versions and adding new lines for the updated item. It also doesn't handle the case where the pro-rated amount is zero (e.g., the change happened at essentially the same time the period started), in which case the old lines should simply be dropped.

On top of that, when there are already outstanding draft invoices for periods that the subscription change affects, those invoices still show the wrong periods and quantities. I'd expect them to be updated automatically — with recalculated usage for usage-based lines — to match the new subscription state.

The trickiest case is when a subscription change would affect an invoice that's already been finalized. Since we can't go back and change those, I'd like a non-blocking warning to be attached to that invoice so operators know there's a discrepancy, rather than having the problem go unnoticed.

Finally, if a draft invoice ends up with no lines left after a subscription change removes everything from it, it should be deleted automatically instead of hanging around empty.

For the warning on finalized invoices, the validation issue should use a specific severity level (warning, not error), a specific error code identifying that immutable invoice handling is not supported, identify the subscription sync process as the source component, and point to the specific line that couldn't be updated.
