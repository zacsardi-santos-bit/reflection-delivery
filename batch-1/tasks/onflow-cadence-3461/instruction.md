Implement the migration logic for Cadence 1.0 capability controllers to correctly handle account link capabilities with unauthorized account reference types. Ensure that migration events are emitted with correctly formatted type strings.

*   Define a package-level variable in `migrations/capcons/linkmigration.go`:
    *   Name: `unauthorizedAccountReferenceStaticType`
    *   Type: `interpreter.NewReferenceStaticType(nil, interpreter.UnauthorizedAccess, interpreter.PrimitiveStaticTypeAccount)`
    *   Purpose: Represent an unauthorized reference to the Account type for subtype compatibility checks.

*   Ensure migration of `PathCapabilityValue` with unauthorized account reference:
    *   For public domain paths:
        *   Emit event: `flow.AccountCapabilityControllerIssued(id: 1, address: 0x0000000000000001, type: Type<auth(Capabilities,Contracts,Inbox,Keys,Storage)&Account>())`
    *   For private domain paths:
        *   Emit the same event as for public paths.

*   Handle migration for chains of path links:
    *   For a public path link with unauthorized account borrow type pointing to a private account link:
        *   Emit for private link: `flow.AccountCapabilityControllerIssued(id: 1, address: 0x0000000000000001, type: Type<auth(Capabilities,Contracts,Inbox,Keys,Storage)&Account>())`
        *   Emit for public link: `flow.AccountCapabilityControllerIssued(id: 2, address: 0x0000000000000001, type: Type<&Account>())`
    *   For a public path link with fully authorized account borrow type pointing to a private account link:
        *   Ensure event type string is: `type: Type<auth(Capabilities,Contracts,Inbox,Keys,Storage)&Account>()`

*   Use `unauthorizedAccountReferenceStaticType` for subtype compatibility checks in link migration logic, replacing any use of `PrimitiveStaticTypeAuthAccount`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.