Implement a utility in a new Go package for translating user and group IDs from container-space to host-space using Linux user namespaces. This utility should handle multiple ID mapping ranges and ensure safe arithmetic operations to prevent overflow errors.

*   Create a new Go package at `internal/userns/` with the package name `userns`.
*   Define a `User` struct in `idmap.go` with:
    *   Fields: `Uid` and `Gid`, both of type `uint32`.
*   Define an `IDMap` struct in `idmap.go` with:
    *   Fields: `UidMap` and `GidMap`, both of type `[]specs.LinuxIDMapping`.
*   Declare a package-level variable `invalidUser` in `idmap.go`:
    *   Type: `User`
    *   Value: `User{Uid: 4294967295, Gid: 4294967295}`.
*   Implement the method `ToHost` for `IDMap` in `idmap.go`:
    *   Signature: `(i IDMap) ToHost(pair User) (User, error)`
    *   Translate container-side `Uid` and `Gid` to host-side values using `UidMap` and `GidMap`.
    *   For each ID to be translated, search the corresponding mapping slice:
        *   If a mapping entry has `ContainerID`, `HostID`, and `Size`, and the container ID is within `[ContainerID, ContainerID+Size)`, compute the host ID as `HostID + (containerID - ContainerID)`.
    *   Return `(invalidUser, error)` if:
        *   The container UID or GID is not covered by any mapping entry.
        *   Computing `ContainerID+Size` would overflow `uint32`.
        *   Computing `HostID + (containerID - ContainerID)` would overflow `uint32`.
        *   The computed host ID equals `4294967295`.
    *   Return `(mappedUser, nil)` on successful translation.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.