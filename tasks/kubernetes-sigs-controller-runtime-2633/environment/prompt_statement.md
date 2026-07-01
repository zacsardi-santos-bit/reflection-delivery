I'm working on a project that uses a fake Kubernetes client for controller testing, and I've run into two bugs that need to be fixed.

The first issue is that after fetching objects using typed Go structs, the fake client is filling in the API version and kind fields on the returned objects. Real Kubernetes clients don't do this for typed objects — those fields stay empty because the type information is already known from the Go type. This inconsistency requires tests to add extra workarounds that wouldn't be needed against a real cluster, and it makes it hard to compare the retrieved object directly to a locally constructed reference.

The second issue is that custom resource types that embed their metadata fields as pointers instead of as value fields don't work with the fake client at all. When you try to perform any operation — getting, listing, patching, updating, or deleting — on such a type, it fails. This is a valid way to define a Kubernetes resource type in Go, and the fake client should handle it just like any other type.

Both of these need to be fixed so that the fake client behaves consistently with real clients and supports the full range of valid resource type definitions.
