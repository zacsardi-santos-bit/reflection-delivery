I'm working on an OpenAPI v3 generator for gRPC services and I need to add support for visibility filtering.

*   The isVisible function must return true when the visibility rule argument is nil (no annotation means always visible).

*   The isVisible function must return false when an annotation is present but no selectors are configured in the registry; it must return true when the annotation's restriction label (or any label in a comma-separated list) matches one of the configured selectors.

*   The OpenAPI generator must exclude from the paths output any method whose visibility restriction label does not match any configured selector.

*   A service-level visibility annotation must cause all methods of that service to be excluded from the generated paths; the service tag must also be omitted from the top-level tags list.

*   A service tag must only be emitted in the tags list when at least one method of that service is visible under the current selectors.

*   Request body inline schemas (for body='*' bindings) must exclude fields whose visibility restriction labels do not match any configured selector; multi-label restrictions such as 'INTERNAL,PREVIEW' count as visible if any one label matches.

*   Component schemas for message types used as named body fields (body='fieldname') must also exclude fields with non-matching visibility restrictions.

*   Query parameters derived from top-level request fields must be excluded when those fields carry non-matching visibility restrictions; nested message fields expanded as dot-separated query parameters must likewise be filtered individually.

*   Enum component schemas must omit values whose visibility restriction labels do not match any configured selector; when all values are filtered out the schema must still be emitted with type='string' and without any enum key (not an empty enum array).

*   OneOf constraints must be built using only the visible fields of each group; if fewer than two visible fields remain in a group, neither a oneOf nor an allOf constraint may be emitted for that group.

*   When no selectors are configured, every annotated element must be hidden and every unannotated element must remain visible.

*   The file protoc-gen-openapiv3/internal/genopenapi/testdata/visibility.prototext must be provided with proto definitions covering: services with method-level and service-level visibility annotations, request messages with field-level visibility annotations (including multi-label), oneof groups with a mix of visible and hidden members, a query request message with nested hidden fields, a body-field message with hidden fields, an enum with a mix of visible and hidden values, and an enum whose every value is hidden.


*   Interface details: Type: Function
Name: isVisible
Location: protoc-gen-openapiv3/internal/genopenapi/visibility.go
Signature: isVisible(r *visibility.VisibilityRule, reg *descriptor.Registry) bool
Description: Reports whether a proto element with the given VisibilityRule should be included in generated output. Returns true when r is nil (no annotation). When r is non-nil, splits r.Restriction on commas and returns true if any trimmed label is present in the registry's configured visibility selectors (as returned by reg.GetVisibilityRestrictionSelectors(), which returns map[string]bool). Returns false if no label matches.

Type: Function
Name: fieldVisibility
Location: protoc-gen-openapiv3/internal/genopenapi/visibility.go
Signature: fieldVisibility(fd *descriptor.Field) *visibility.VisibilityRule
Description: Extracts the google.api.field_visibility VisibilityRule from a field's options. Returns nil if not present.

Type: Function
Name: serviceVisibility
Location: protoc-gen-openapiv3/internal/genopenapi/visibility.go
Signature: serviceVisibility(svc *descriptor.Service) *visibility.VisibilityRule
Description: Extracts the google.api.api_visibility VisibilityRule from a service's options. Returns nil if not present.

Type: Function
Name: methodVisibility
Location: protoc-gen-openapiv3/internal/genopenapi/visibility.go
Signature: methodVisibility(m *descriptor.Method) *visibility.VisibilityRule
Description: Extracts the google.api.method_visibility VisibilityRule from a method's options. Returns nil if not present.

Type: Function
Name: enumValueVisibility
Location: protoc-gen-openapiv3/internal/genopenapi/visibility.go
Signature: enumValueVisibility(v *descriptorpb.EnumValueDescriptorProto) *visibility.VisibilityRule
Description: Extracts the google.api.value_visibility VisibilityRule from an enum value's options. Returns nil if not present.

Type: File
Name: visibility.prototext
Location: protoc-gen-openapiv3/internal/genopenapi/testdata/visibility.prototext
Description: Proto text format CodeGeneratorRequest fixture used by visibility tests. Must define package "vis.v1" with:
- PublicRequest message: fields publicField (no annotation), internalField (INTERNAL), previewField (PREVIEW), multiField (INTERNAL,PREVIEW)
- OneofRequest message: oneof group with visibleChoice (no annotation), internalChoice (INTERNAL), previewChoice (PREVIEW)
- OneofCollapseRequest message: oneof group with lonelyVisible (no annotation), lonelyInternal (INTERNAL)
- QueryRequest message: visibleQ (no annotation), internalQ (INTERNAL), filter (message type NestedFilter)
- NestedFilter message: label (no annotation), secretLabel (INTERNAL)
- BodyPayload message: title (no annotation), hiddenTitle (INTERNAL)
- BodyFieldRequest message: id, payload (type BodyPayload)
- AllHiddenHolder message: hiddenState (type AllHiddenEnum)
- Status enum: STATUS_UNSPECIFIED (0), STATUS_ACTIVE (1), STATUS_INTERNAL_ONLY (2, INTERNAL)
- AllHiddenEnum enum: ALL_HIDDEN_UNSPECIFIED (0, INTERNAL), ALL_HIDDEN_A (1, INTERNAL)
- PublicService with methods: PublicMethod (POST /v1/public, body="*", input PublicRequest), InternalMethod (GET /v1/internal, INTERNAL restriction), PreviewMethod (GET /v1/preview, PREVIEW restriction), OneofMethod (POST /v1/oneof, body="*", input OneofRequest), OneofCollapseMethod (POST /v1/oneof_collapse, body="*", input OneofCollapseRequest), QueryMethod (GET /v1/query, input QueryRequest), BodyFieldMethod (POST /v1/body_field/{id}, body="payload", input BodyFieldRequest), AllHiddenEnumMethod (POST /v1/all_hidden_enum, body="*", input AllHiddenHolder)
- InternalService (api_visibility INTERNAL) with SecretMethod (GET /v1/secret)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.