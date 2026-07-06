I'm running into a peer dependency resolution bug in pnpm.

*   The resolvePeers function must handle the 'peer diamond' scenario: when a package peer-depends on both package A and package B, and package A also peer-depends on package B, the resolver must ensure A and B resolve to a mutually consistent version of their shared peer.

*   When a nested plugin peer-depends on both a parser and a shared runtime, and the parser also peer-depends on that runtime, the resulting dependenciesGraph must contain the dep path where both the plugin and its parser resolve to the same runtime version (e.g., 'plugin/1.0.0(parser/1.0.0(typescript/1.0.0))(typescript/1.0.0)').

*   The dependenciesGraph must NOT contain a dep path where the plugin's parser resolves to a different runtime version than the plugin itself (e.g., the path 'plugin/1.0.0(parser/1.0.0(typescript/2.0.0))(typescript/1.0.0)' must be absent).

*   When a top-level instance of a package (e.g., parser resolved against runtime@2.0.0) conflicts with a nested instance of the same package (e.g., parser resolved against runtime@1.0.0 within an app subtree), the nested sibling package that peer-depends on both must use the locally consistent instance, not the top-level hoisted instance.

*   An end-to-end install of packages forming the peer diamond pattern must produce a lockfile containing exactly one snapshot for the plugin package: the snapshot where all shared peers are consistent with the plugin's own peer resolutions.


*   Interface details: Type: Function
Name: resolvePeers
Location: installing/deps-resolver/src/resolvePeers.ts
Signature: resolvePeers(opts: ResolvePeersOptions) -> Promise<{ dependenciesGraph: DependenciesGraph, ... }>
Description: Resolves peer dependencies across the entire dependency tree. Given a dependency tree map and project roots, computes the deduplicated dependency graph with peer-resolved suffixes as keys. Must be fixed so that when a package peer-depends on two packages A and B, and A also peer-depends on B, the resolved dep path for that package always shows A and B agreeing on the same version of their shared peer B — not an inherited instance of A that resolved B at a different version.

Parameters accepted in the options object:
- allPeerDepNames: Set<string> — names of all peer dependencies in the graph
- projects: Array of project descriptors, each with directNodeIdsByAlias (Map<string, NodeId>), topParents (array), rootDir (ProjectRootDir), and id (string)
- resolvedImporters: object
- dependenciesTree: Map<NodeId, DependenciesTreeNode<PartialResolvedPackage>> — the full dependency tree; each node has children (Record<string, NodeId>), installable (boolean), resolvedPackage (PartialResolvedPackage with name, pkgIdWithPatchHash, version, peerDependencies, id fields), and depth (number)
- virtualStoreDir: string
- virtualStoreDirMaxLength: number
- lockfileDir: string
- peersSuffixMaxLength: number
- workspaceProjectIds: Set<string>

Return value: Promise resolving to an object containing dependenciesGraph, whose keys are peer-resolved dep path strings of the form 'pkg/version(peer1/version(peer2/version))(peer3/version)'.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.