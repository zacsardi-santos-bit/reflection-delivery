I'm working with the go-github library and I need to add support for Dependabot alert webhook events. Right now, when GitHub sends a Dependabot alert webhook — for example when a vulnerability is created, dismissed, or resolved in a repository — the library has no structured event type to represent it. I'd like to be able to parse these webhooks into a typed Go struct that includes the action, the alert details, and context about the repository, organization, enterprise, sender, and installation.

I also noticed that the existing Dependabot alert data model is missing a field for when an alert was automatically dismissed. That field should be added too, along with a nil-safe accessor consistent with the rest of the library.

The webhook parsing infrastructure should recognize the new event type by its string identifier so that callers get back the right type when parsing incoming payloads. The hook delivery mapping should also include it. Could you add this support to the library?
