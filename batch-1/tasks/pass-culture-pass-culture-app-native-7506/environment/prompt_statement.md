We've been gradually rolling out a redesigned navigation tab bar in our app behind a feature flag, and it's now ready to ship as the permanent default. I need to remove that old tab bar redesign flag from all the relevant navigation components so the updated design is always rendered without any flag needing to be enabled.

Right now, if someone enables only the reactions feature flag in tests or during development, the navigation components don't render correctly — they still fall back to the old appearance because both the tab bar flag and the reactions flag had to be enabled together. This is fragile and confusing. I want to clean it up so the reactions feature flag works correctly on its own, with the updated tab bar design simply being the default.

Can you remove the dependency on the old tab bar redesign flag from the navigation components? The flag entry itself should also be deleted from the feature flags registry so it's fully gone from the codebase.
