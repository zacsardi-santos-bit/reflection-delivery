Implement a new endpoint in the Prowler API to summarize affected cloud services and regions by security findings, with optional filters for date and severity. Ensure the endpoint returns distinct services and regions, handles invalid date formats with specific error messages, and provides empty lists when no findings match the filters.

*   Add a new GET action to the FindingViewSet:
    *   URL name: 'finding-findings_services_regions'.
    *   Return distinct services and regions from matching findings.
    *   Use JSON:API format for responses:
        *   'data.type' must be 'finding-dynamic-filters'.
        *   'data.id' must be null.
        *   'data.attributes' must include 'services' and 'regions' as lists of strings.
    *   Support filtering by:
        *   Date using 'filter[inserted_at]'.
        *   Severity using 'filter[severity__in]'.
    *   Return empty lists for 'services' and 'regions' when no findings match the filters.

*   Handle invalid date formats:
    *   Return HTTP 400 with error structure:
        *   {"errors": [{"detail": "Enter a valid date.", "status": "400", "source": {"pointer": "/data/attributes/inserted_at"}, "code": "invalid"}]}.

*   Create a new serializer 'FindingDynamicFilterSerializer' in 'api/src/backend/api/v1/serializers.py':
    *   Fields:
        *   'services': ListField(child=CharField(), allow_empty=True).
        *   'regions': ListField(child=CharField(), allow_empty=True).
    *   Meta class:
        *   Set 'resource_name' to 'finding-dynamic-filters'.

*   Implement the 'findings_services_regions' method in the FindingViewSet class in 'api/src/backend/api/v1/views.py':
    *   Decorate with @action(detail=False, methods=['get'], url_name='findings_services_regions').
    *   Use FindingDynamicFilterSerializer for this action.
    *   Return HTTP 200 with serialized data on success.
    *   Return HTTP 400 with JSON:API-formatted error on invalid input.

*   Override 'get_serializer_class' in FindingViewSet:
    *   Return FindingDynamicFilterSerializer when self.action == "findings_services_regions".
    *   Delegate to the parent implementation otherwise.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.