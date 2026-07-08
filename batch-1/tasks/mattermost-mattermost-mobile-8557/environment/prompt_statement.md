I'm adding custom profile attribute support to the mobile app's edit-profile screen and right now it's just broken from a feature-parity standpoint. Even when the server has custom attributes configured (stuff like department, phone extension, whatever the admin defines), those fields never show up in the edit form, so mobile users literally can't see or change them, which is annoying because the web client handles this fine.

What I want: when the screen opens, load the current custom attribute values from the server and render each one as an editable text field right alongside the standard profile fields, and when someone edits a value and hits save, push that back to the server so it persists.

To make that work I need a new remote action that fetches both the attribute field definitions and the user's current values and merges them into one thing, plus another action that sends the updated values back up. The REST client is also missing a method to update custom attribute values via a PATCH request, so add that too.

Also the form builds its field list dynamically now, so the old individual static refs for focus management don't scale. I want a reusable hook for managing a dynamic keyed collection of focusable field references instead.

Oh and the edit-profile form component should conditionally render these custom inputs off a feature flag prop, when the flag's off they don't appear at all, and when it's on but there just aren't any custom attributes defined, still nothing shows. When it's on and there are attributes, render each with appropriate identifiers (keyed per attribute) so edits route back to the right attribute in state.
