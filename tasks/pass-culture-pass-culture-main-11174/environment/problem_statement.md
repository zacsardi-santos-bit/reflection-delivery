## Description

The back-office administration panel needs a new section for managing offer price limitation rules. These rules define how much an offer's price is allowed to vary within a given subcategory. Currently there is no dedicated interface for viewing, creating, editing, or deleting these rules, making it impossible for administrators to manage them through the back-office.

## Expected Behavior

- Administrators with fraud management permissions can access a list of all existing price limitation rules
- The list displays each rule's ID, the category and subcategory it applies to, and the allowed price variation percentage
- Administrators can filter the list by category or by subcategory
- Administrators can create a new rule by selecting a subcategory and entering a percentage value
- Attempting to create a rule for a subcategory that already has one results in an error message, and no duplicate is created
- Administrators can edit the percentage value of an existing rule
- Submitting an invalid percentage (e.g., an unreasonably large value) is rejected and the rule is left unchanged
- Administrators can delete a rule permanently, with appropriate confirmation messaging

## Why This Matters

Without this interface, fraud administrators have no way to view or manage price limitation rules through the back-office. This feature brings the management of these rules in line with other fraud and compliance tools already available in the administration panel.
