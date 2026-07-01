Enforce that every segment in Chroma's data model must always have an associated collection by updating the codebase to treat the collection field as required. Ensure the segment's collection is never null or missing in its network representation and update related logic to reflect this requirement.

*   Update the function `convertSegmentToProto` in `go/pkg/coordinator/grpc/proto_model_convert.go`:
    *   Ensure the `Collection` field of the returned protobuf struct is always a non-empty string.
    *   Set the `Collection` field to the nil UUID string ("00000000-0000-0000-0000-000000000000") when `segment.CollectionID` is nil.

*   Modify the `Segment` struct in `go/pkg/proto/coordinatorpb/chroma.pb.go`:
    *   Change the `Collection` field to be a non-optional string: `Collection string 'protobuf:"bytes,5,opt,name=collection,proto3" json:"collection,omitempty"'`.

*   Update the proto IDL definition in `idl/chromadb/proto/chroma.proto`:
    *   Change the `collection` field in the `Segment` message to a required string: `string collection = 5;`.

*   Ensure segments always have a non-null collection UUID:
    *   Prevent creation or updates of segments with a null collection.
    *   Use attribute-style access for collection objects during segment construction (e.g., `object.id`).

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.