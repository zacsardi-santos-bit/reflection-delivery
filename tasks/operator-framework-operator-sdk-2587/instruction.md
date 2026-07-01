Implement support for referencing Ansible roles using the fully-qualified collection name (FQCN) format in the Ansible operator watches system. Update the role path resolution logic to accommodate roles distributed as part of Ansible collections, allowing for multiple search paths and configurable environment variables.

*   Update the `getPossibleRolePaths` function:
    *   Change the return type to a slice of strings (`[]string`) to include all candidate paths.
    *   For a simple role name or relative path, include the path formed by joining the current working directory's 'roles' subdirectory with that name.
    *   If `ANSIBLE_ROLES_PATH` is set, include paths `<entry>/<name>` and `<entry>/roles/<name>` for each entry in the colon-separated list.
    *   For a fully-qualified collection name (three dot-separated segments):
        *   If `ANSIBLE_COLLECTIONS_PATH` is not set, include paths under standard Ansible collection directories: `/usr/share/ansible/collections/ansible_collections/NS/Col/roles/Role` and `~/.ansible/collections/ansible_collections/NS/Col/roles/Role`.
        *   If `ANSIBLE_COLLECTIONS_PATH` is set, include only paths based on this variable and the default cwd/roles path.
    *   Handle absolute or empty paths by returning a slice containing that path unchanged.
    *   Ensure system/user default collection paths are not included when `ANSIBLE_COLLECTIONS_PATH` is set.

*   Modify the `Load` function:
    *   Return an error if a watches file references a role using FQCN and `ANSIBLE_COLLECTIONS_PATH` is not set, and the role does not exist at default locations.
    *   Ensure successful loading when a collection role is found via `ANSIBLE_COLLECTIONS_PATH`.

*   Update test data and templates:
    *   Create `testdata/invalid_collection.yaml` with a watch entry referencing a non-existent collection role.
    *   Add an entry to `testdata/valid.yaml.tmpl` with version=v1alpha1, group=app.example.com, kind=AnsibleCollectionEnvTest, and role=nameSpace.collection.someRole.
    *   Ensure `valid.yaml` resolves the Role field to the correct path when `ANSIBLE_COLLECTIONS_PATH` is set.
    *   Ensure the directory `pkg/ansible/watches/testdata/ansible_collections/nameSpace/collection/roles/someRole/` exists with at least one file.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.