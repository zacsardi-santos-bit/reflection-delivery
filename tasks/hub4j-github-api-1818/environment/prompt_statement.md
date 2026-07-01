I'm building a GitHub webhook handler using this Java library and I'm running into several gaps in event payload support.

First, when I receive a membership event (someone being added to or removed from a team), there's no model class to parse that webhook payload — I can't get the team, the member, or the organization from it.

Second, team lifecycle events (like when a team is created or edited) also have no model class. For edit events especially, I need to know what changed — was it the description? The name? The privacy setting? The permissions on a linked repository? I need a structured "changes" object that tells me the previous value for whatever was modified.

Third, the existing member event class doesn't expose what changed. When a collaborator's permission is updated on a repository, I can see the event happened, but I can't see what the old permission was or what the new permission is.

Fourth, for team-add events, the parsed team object is missing key fields like its node ID, description, and privacy setting — and it's not linked back to the organization.

On top of all that, the library currently crashes if GitHub sends a privacy value or permission level it doesn't recognize. It should instead fall back to a safe unknown/default value so my application doesn't break.

Could you add support for these event payload types and fix the fragile enum handling?
