Add support for Dependabot alert webhook events to the go-github library. Implement a new structured event type for these webhooks and update the alert data model to include a field for automatic dismissal timestamps. Ensure the webhook parsing infrastructure recognizes this new event type.

*   Define a new `DependabotAlertEvent` struct in `github/event_types.go` with fields:
    *   `Action` (*string, json: 'action')
    *   `Alert` (*DependabotAlert, json: 'alert')
    *   `Repo` (*Repository, json: 'repository')
    *   `Organization` (*Organization, json: 'organization')
    *   `Enterprise` (*Enterprise, json: 'enterprise')
    *   `Sender` (*User, json: 'sender')
    *   `Installation` (*Installation, json: 'installation')
*   Ensure `DependabotAlertEvent` supports JSON marshaling/unmarshaling:
    *   An empty instance should serialize to '{}'.
    *   A fully-populated instance should marshal and unmarshal correctly.
*   Add a new field `AutoDismissedAt` to `DependabotAlert`:
    *   Type: *Timestamp
    *   JSON tag: 'auto_dismissed_at'
*   Implement `GetAutoDismissedAt()` in `github/github-accessors.go`:
    *   Signature: `(d *DependabotAlert) GetAutoDismissedAt() Timestamp`
    *   Return zero Timestamp when the field is nil.
    *   Ensure it is safe to call on a nil *DependabotAlert receiver.
*   Add nil-safe getter methods to `*DependabotAlertEvent` in `github/github-accessors.go`:
    *   `GetAction() string`
    *   `GetAlert() *DependabotAlert`
    *   `GetEnterprise() *Enterprise`
    *   `GetInstallation() *Installation`
    *   `GetOrganization() *Organization`
    *   `GetRepo() *Repository`
    *   `GetSender() *User`
    *   Ensure all methods are safe to call on a nil receiver.
*   Update webhook parsing logic in `github/messages.go`:
    *   Map the message type string 'dependabot_alert' to `*DependabotAlertEvent`.
*   Update `hookDeliveryPayloadTypeToStruct` map in `github/repos_hooks_deliveries.go`:
    *   Include the key 'dependabot_alert' mapping to `&DependabotAlertEvent{}`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.