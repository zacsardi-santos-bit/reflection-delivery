I'm working on the SCIM server implementation and have found a few related problems that need to be fixed together.

*   The SCIM Error type must implement JSON deserialization so that the 'status' field, which is serialized as a quoted string (e.g., '"400"'), can be parsed back to an integer. An empty or absent 'status' value must result in a zero integer with no error. A non-numeric status string must return a parse error. Invalid JSON must also return an error.

*   Serializing an Error value to JSON and then deserializing it back must produce an identical value, preserving all fields including Schemas, Status, Detail, and ScimType.

*   The syncGroupMembers function must perform a pre-flight validation of all members before applying any mutations. Members with type 'Group' (case-insensitive) must be rejected with a 400 Bad Request SCIM error. Members with any other unrecognized type must be rejected with a 400 Bad Request SCIM error. Members whose user identifier is not found in the user cache must be rejected with a 404 Not Found SCIM error. Only after all members pass validation may any group membership changes be applied.

*   Because syncGroupMembers performs a pre-flight existence check followed by a membership mutation that also looks up the user, the user cache will be called twice per new member being added: once during pre-flight and once during the actual add operation.

*   The PatchGroup handler must validate members in 'add' operations before applying any changes. Adding a member with type 'Group' must return HTTP 400 with a SCIM error response body. Adding a member with an unrecognized type must return HTTP 400 with a SCIM error response body. Adding a member whose user identifier does not exist must return HTTP 404 with a SCIM error response body.

*   The ensureRancherGroup function, when locating an existing group by its internal ID, must compare the stored ExternalID to the requested ExternalID. If they differ, the group must be updated via the group client to persist the new ExternalID before returning.

*   The UpdateGroup handler must persist ExternalID changes. When an update request supplies a different externalId from what is currently stored, the handler must call the group update API and include the updated externalId value in the 200 OK response body.

*   All SCIM error responses from group and user endpoints (CreateGroup, GetGroup, UpdateGroup, DeleteGroup, PatchGroup, CreateUser, GetUser, UpdateUser, DeleteUser, ListUsers) must include a JSON response body that, when deserialized as a SCIM Error, has a Schemas field containing the SCIM error schema URN ('urn:ietf:params:scim:api:messages:2.0:Error') and a Status field equal to the HTTP response status code.

*   The ListUsers handler must return HTTP 500 with a SCIM error body when the user attribute cache returns any error other than 'not found' for a given user. A 'not found' error must continue to cause the user to be skipped without an error response.

*   The GetUser handler must return HTTP 500 with a SCIM error body when the user attribute cache fails to retrieve attributes for the requested user.


*   Interface details: Type: Method
Name: UnmarshalJSON
Location: pkg/auth/providers/scim/response.go
Signature: (e *Error) UnmarshalJSON(data []byte) error
Description: Deserializes a SCIM Error from JSON. The SCIM protocol serializes the status code as a quoted string (e.g., "400"), so this method must parse that string back to the integer Status field. An empty or missing status field must set Status to 0 without returning an error. A non-numeric status string must return an error. Invalid JSON must return an error. The Schemas, Detail, and ScimType fields should be deserialized normally.

Type: Struct
Name: Error
Location: pkg/auth/providers/scim/response.go
Description: SCIM error response type. Has fields: Schemas []string, Status int, Detail string, ScimType string. The MarshalJSON method serializes Status as a quoted string. The new UnmarshalJSON method deserializes it back. The constant errorSchemaID ("urn:ietf:params:scim:api:messages:2.0:Error") must appear in the Schemas field of all error responses.

Type: Method
Name: syncGroupMembers
Location: pkg/auth/providers/scim/group.go
Signature: (s *SCIMServer) syncGroupMembers(provider, groupName string, members []scimMember) error
Description: Synchronizes group members. Must perform a pre-flight validation pass over all members before making any mutations. Member type "group" (case-insensitive) must return *Error with Status 400. Any unrecognized member type must return *Error with Status 400. A member whose Value is not found in userCache must return *Error with Status 404. Only after all members pass validation are additions and removals applied. Because pre-flight calls userCache.Get and addGroupMember also calls userCache.Get, the cache is accessed twice per new member.

Type: Method
Name: PatchGroup
Location: pkg/auth/providers/scim/group.go
Signature: (s *SCIMServer) PatchGroup(w http.ResponseWriter, r *http.Request)
Description: Handles PATCH requests for groups. For "add" member operations: member type "group" must return HTTP 400 with SCIM error body (Schemas contains errorSchemaID, Status 400). Unrecognized member type must return HTTP 400. Pre-flight: must verify each member to add exists in userCache before applying any mutation; member not found must return HTTP 404 with SCIM error body.

Type: Method
Name: ensureRancherGroup
Location: pkg/auth/providers/scim/group.go
Signature: (s *SCIMServer) ensureRancherGroup(provider string, grp scimGroup) (*v3.Group, bool, error)
Description: Ensures a Rancher group exists for the given SCIM group. When an existing group is found by its internal ID (grp.ID != ""), must compare the stored ExternalID to grp.ExternalID. If they differ, must update the group via groups.Update with the new ExternalID before returning. Returns (group, created=false, nil) for existing groups.

Type: Method
Name: UpdateGroup
Location: pkg/auth/providers/scim/group.go
Signature: (s *SCIMServer) UpdateGroup(w http.ResponseWriter, r *http.Request)
Description: Handles PUT requests for groups. When the group's ExternalID changes, must persist the update (via ensureRancherGroup calling groups.Update). The response body must include "externalId" with the updated value. Returns HTTP 200 on success.

Type: Method
Name: ListUsers
Location: pkg/auth/providers/scim/user.go
Signature: (s *SCIMServer) ListUsers(w http.ResponseWriter, r *http.Request)
Description: Handles GET requests for user listing. When userAttributeCache.Get returns an error that is not a "not found" error, must return HTTP 500 with a SCIM error body. "Not found" errors must continue to cause the user to be skipped silently.

Type: Method
Name: GetUser
Location: pkg/auth/providers/scim/user.go
Signature: (s *SCIMServer) GetUser(w http.ResponseWriter, r *http.Request)
Description: Handles GET requests for individual users. When userAttributeCache.Get returns any error, must return HTTP 500 with a SCIM error body (Schemas contains errorSchemaID, Status 500).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.