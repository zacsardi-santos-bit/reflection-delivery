I'm working with the interface bulk rename feature in NetBox and I'd like it to support renaming multiple fields — specifically both the interface name and the interface label — rather than only targeting a single implicit field.

*   InterfaceBulkRenameView must define a rename_fields class attribute equal to ('name', 'label'), enabling bulk rename operations to target either the interface name field, the interface label field, or both simultaneously.

*   When InterfaceBulkRenameView has rename_fields defined and the POST data includes field_names=['name'], the bulk rename preview step (POST with _preview='1' and _all='1') must return HTTP 200 and populate selected_objects in the response context with all matching interface objects regardless of pagination.

*   When InterfaceBulkRenameView has rename_fields defined and the POST data includes field_names=['name'] along with a pk list, the bulk rename apply step (POST with _apply='1' and _all='1') must successfully rename the matching interfaces using the provided find/replace pattern and return an HTTP redirect on success.

*   When rename_fields is defined on the bulk rename view and no valid field_names is submitted in the POST data, the view must add a form validation error (the base BulkRenameView already implements this check — the requirement is that rename_fields is set so the check is triggered).


*   Interface details: Type: Class
Name: InterfaceBulkRenameView
Location: netbox/dcim/views.py
Description: View for bulk renaming of Interface objects. Must define a rename_fields class attribute set to the tuple ('name', 'label'), enabling users to select which field(s) to rename in bulk. This attribute activates field-selection validation in the base BulkRenameView, requiring that POST requests include a field_names parameter specifying one or more fields from the rename_fields tuple.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.