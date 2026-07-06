I'm working on extending NetBox's IPAM module so that Autonomous System Numbers (ASNs) can be assigned a functional role, just like prefixes and VLANs already can.

*   The ASN model must support an optional 'role' field that stores a ForeignKey reference to the Role model (in ipam). The field must be nullable and blank (optional), use SET_NULL on deletion, and have a reverse relation named 'asns'.

*   A new Django migration must be created in netbox/ipam/migrations/ that adds the 'role' ForeignKey field to the ASN model.

*   The ASNFilterSet must support two new filter parameters: 'role_id' (filter by a list of Role PKs) and 'role' (filter by a list of Role slugs). Filtering by two out of three roles must return all ASNs assigned to those roles.

*   The Role API serializer's brief_fields must include 'asn_count' alongside the existing fields. The expected brief_fields set is: 'asn_count', 'description', 'display', 'id', 'name', 'prefix_count', 'slug', 'url', 'vlan_count'.

*   The Role API serializer must expose an 'asn_count' field (using RelatedObjectCountField with the 'asns' reverse relation) in its full field list as well.

*   The ASN API serializer must include and accept a 'role' field (nested Role representation) in its fields list, supporting read, create, and update operations via Role PK.

*   The ASN web form must include a 'role' field so that creating or editing an ASN via the UI can assign a role by PK.

*   The ASN bulk edit form must include a 'role' field, allowing bulk assignment or clearing of the role across multiple ASNs.

*   The ASN CSV import form must support a 'role' column that matches roles by their name (to_field_name='name'), making the role field optional in CSV import.


*   Interface details: Type: Model Field
Name: ASN.role
Location: netbox/ipam/models/asns.py
Signature: role = models.ForeignKey(to='ipam.Role', on_delete=models.SET_NULL, related_name='asns', blank=True, null=True)
Description: Optional ForeignKey from ASN to Role. The related_name MUST be 'asns' because the RoleSerializer uses RelatedObjectCountField('asns') to compute asn_count. On_delete must be SET_NULL.

Type: Migration
Name: 0087_add_asn_role (or next sequential migration number)
Location: netbox/ipam/migrations/
Description: Django migration that adds the 'role' ForeignKey field to the ASN model. Must depend on the previous ipam migration.

Type: FilterSet Parameters
Name: ASNFilterSet.role_id and ASNFilterSet.role
Location: netbox/ipam/filtersets.py
Signature:
  role_id: ModelMultipleChoiceFilter(queryset=Role.objects.all()) — filters by Role primary key
  role: ModelMultipleChoiceFilter(field_name='role__slug', queryset=Role.objects.all(), to_field_name='slug') — filters by Role slug
Description: Two new filter parameters on ASNFilterSet. 'role_id' accepts a list of Role PKs; 'role' accepts a list of Role slugs. Both must filter the ASN queryset to only ASNs assigned to the matching roles.

Type: Serializer Field
Name: RoleSerializer.asn_count
Location: netbox/ipam/api/serializers_/roles.py
Signature: asn_count = RelatedObjectCountField('asns')
Description: Computed field that returns the count of ASNs assigned to this role. Must appear in the serializer's fields list AND in brief_fields. The brief_fields tuple must include: 'id', 'url', 'display', 'name', 'slug', 'description', 'prefix_count', 'vlan_count', 'asn_count'.

Type: Serializer Field
Name: ASNSerializer.role
Location: netbox/ipam/api/serializers_/asns.py
Signature: role = RoleSerializer(nested=True, required=False, allow_null=True)
Description: Nested role field on the ASN API serializer. Must be included in the Meta.fields list. Accepts a Role PK for write operations.

Type: Form Field
Name: ASNForm.role
Location: netbox/ipam/forms/model_forms.py
Description: DynamicModelChoiceField for Role, optional, added to the ASN create/edit form. Must be included in the Meta.fields list for the ASNForm.

Type: Form Field
Name: ASNBulkEditForm.role
Location: netbox/ipam/forms/bulk_edit.py
Description: DynamicModelChoiceField for Role, optional, added to the ASN bulk edit form. Must be included in nullable_fields so it can be cleared.

Type: Form Field
Name: ASNImportForm.role
Location: netbox/ipam/forms/bulk_import.py
Signature: role = CSVModelChoiceField(queryset=Role.objects.all(), required=False, to_field_name='name')
Description: CSV import field for role, matched by role name (to_field_name='name'). Must be included in the Meta.fields tuple for ASNImportForm. The CSV column header is 'role'.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.