Implement support for AWS Simple Email Service (SES) resources in cloud-nuke to enable automatic discovery and deletion of SES Configuration Sets, Receipt Rule Sets, Receipt Filters, Email Templates, and Identities. Integrate these resources with existing filtering capabilities, allowing exclusion by name pattern and creation time where applicable.

*   Update the `SesConfigurationSet` struct in `aws/resources/ses_configuration_set.go`:
    *   Add a `Client` field for the SES API interface.
    *   Implement `getAll(ctx context.Context, config config.Config) ([]*string, error)` to list all SES configuration sets, filtering by `config.SESConfigurationSet` using `ExcludeRule.NamesRegExp`.
    *   Implement `nukeAll(identifiers []*string) error` to delete specified SES configuration sets.

*   Update the `SesReceiptRule` struct in `aws/resources/ses_email_receiving.go`:
    *   Add a `Client` field for the SES API interface.
    *   Implement `getAll(ctx context.Context, config config.Config) ([]*string, error)` to list all SES receipt rule sets, filtering by `config.SESReceiptRuleSet` using both `ExcludeRule.NamesRegExp` and `ExcludeRule.TimeAfter`.
    *   Implement `nukeAll(identifiers []*string) error` to delete specified SES receipt rule sets.

*   Update the `SesReceiptFilter` struct in `aws/resources/ses_email_receiving.go`:
    *   Add a `Client` field for the SES API interface.
    *   Implement `getAll(ctx context.Context, config config.Config) ([]*string, error)` to list all SES receipt filters, filtering by `config.SESReceiptFilter` using `ExcludeRule.NamesRegExp`.
    *   Implement `nukeAll(identifiers []*string) error` to delete specified SES receipt filters.

*   Update the `SesEmailTemplates` struct in `aws/resources/ses_email_templates.go`:
    *   Add a `Client` field for the SES API interface.
    *   Implement `getAll(ctx context.Context, config config.Config) ([]*string, error)` to list all SES email templates, filtering by `config.SESEmailTemplates` using both `ExcludeRule.NamesRegExp` and `ExcludeRule.TimeAfter`.
    *   Implement `nukeAll(identifiers []*string) error` to delete specified SES email templates.

*   Update the `SesIdentities` struct in `aws/resources/ses_identity.go`:
    *   Add a `Client` field for the SES API interface.
    *   Implement `getAll(ctx context.Context, config config.Config) ([]*string, error)` to list all SES identities, filtering by `config.SESIdentity` using `ExcludeRule.NamesRegExp`.
    *   Implement `nukeAll(identifiers []*string) error` to delete specified SES identities.

*   Update the `Config` struct in `config/config.go`:
    *   Add `SESConfigurationSet`, `SESReceiptRuleSet`, `SESReceiptFilter`, `SESEmailTemplates`, and `SESIdentity` fields, each initialized as an empty `ResourceType`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.