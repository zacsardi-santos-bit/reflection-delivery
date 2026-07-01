## Description

The Shadow component currently only supports a set of generic depth values that apply neutral shadows — there is no way to specify brand-colored shadow variants for elements displayed on brand-colored backgrounds. In addition, the depth value naming convention is inconsistent with the broader design system, which uses prefixed names that distinguish standard from brand shadows.

## Expected Behavior

- The Shadow component should accept a depth value that covers both neutral and brand shadow variants at each depth level (2, 4, 8, 16, 28, 64).
- The naming convention for depth values should be updated so that each depth is clearly distinguishable as either a neutral shadow or a brand shadow variant.
- Brand shadow variants should use different opacity characteristics than their neutral counterparts to ensure they look correct on brand-colored backgrounds.

## Why This Matters

Designers using this component system need to apply shadows that match design system specifications for both neutral and brand surfaces. Without brand-specific shadow variants, developers are forced to work around the component or accept incorrect-looking shadows on brand-colored elements. Updating the depth naming convention also prevents confusion when using depth values that previously did not indicate their shadow type.
