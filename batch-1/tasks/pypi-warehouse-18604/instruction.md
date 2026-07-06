Implement an admin-only feature to manually activate organizations on PyPI without requiring the standard billing setup. Ensure that admins can manage these activations through the organization admin detail page, allowing them to add, update, and remove manual activations with specified seat limits and expiration dates.

*   Create a new `OrganizationManualActivation` model in `warehouse/organizations/models.py` with the following fields:
    *   `organization_id`: UUID, primary key, foreign key to organizations with CASCADE delete.
    *   `seat_limit`: Integer.
    *   `expires`: Date.
    *   `created`: Datetime, server default now.
    *   `created_by_id`: UUID, foreign key to users.
    *   Relationships back to `Organization` and `User`.
    *   Properties:
        *   `is_active`: Returns True if today's date is strictly less than `expires`.
        *   `current_member_count`: Returns the count of roles associated with the organization.
        *   `has_available_seats`: Returns True if `current_member_count` is less than `seat_limit`.
        *   `available_seats`: Returns max(0, `seat_limit` - `current_member_count`).

*   Update the `Organization` model in `warehouse/organizations/models.py`:
    *   Add an `is_in_good_standing()` method and a `good_standing` property. 
        *   For Community-type organizations, return True if active.
        *   For Company-type organizations, return True if active and having either an active subscription or manual activation.

*   Add a `ManualActivationForm` class in `warehouse/admin/views/organizations.py`:
    *   Fields:
        *   `seat_limit`: IntegerField, required with error message "Specify seat limit", minimum value 1 with error message "Seat limit must be at least 1".
        *   `expires`: DateField, required with error message "Specify expiration date", must be strictly in the future with error message "Expiration date must be in the future".

*   Implement view functions in `warehouse/admin/views/organizations.py`:
    *   `add_manual_activation(request) -> HTTPSeeOther`:
        *   Raise `HTTPNotFound` if the organization is not found.
        *   Flash error "already has manual activation" if an activation exists.
        *   Validate form and flash errors as "{field}: {error}" on failure.
        *   On success, create `OrganizationManualActivation`, record an event, flash success message, and return `HTTPSeeOther`.
    *   `update_manual_activation(request) -> HTTPSeeOther`:
        *   Raise `HTTPNotFound` if the organization is not found.
        *   Flash error "has no manual activation to update" if none exists.
        *   Validate form and flash errors on failure.
        *   On success, update `seat_limit` and `expires`, record an event, flash success message, and return `HTTPSeeOther`.
    *   `delete_manual_activation(request) -> HTTPSeeOther`:
        *   Raise `HTTPNotFound` if the organization is not found.
        *   Flash error "has no manual activation to delete" if none exists.
        *   Require POST parameter "confirm" matching the organization name; flash "Confirm the request" if not matched.
        *   On success, delete activation, record an event, flash success message, and return `HTTPSeeOther`.

*   Register routes in `warehouse/admin/routes.py`:
    *   'admin.organization.add_manual_activation' at '/admin/organizations/{organization_id}/add_manual_activation/'.
    *   'admin.organization.update_manual_activation' at '/admin/organizations/{organization_id}/update_manual_activation/'.
    *   'admin.organization.delete_manual_activation' at '/admin/organizations/{organization_id}/delete_manual_activation/'.

*   Update `ActiveOrganizationPredicate` in `warehouse/predicates.py` to use `is_in_good_standing()`.
    *   Organizations with an active manual activation must satisfy the predicate.
    *   Redirect to 'manage.organizations' if only an expired manual activation exists.

*   Modify the manage organization roles view in `warehouse/manage/views/organizations.py`:
    *   On POST request to invite a new member to a Company organization not in good standing, flash "Cannot invite new member. Organization is not in good standing." and return `HTTPSeeOther`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.