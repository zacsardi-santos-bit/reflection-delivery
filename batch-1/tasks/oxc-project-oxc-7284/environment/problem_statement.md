## Description

The transformer incorrectly associates the compound logical assignment operators with the wrong ECMAScript year. These operators — which combine logical operations with assignment — were standardized in ES2021, but the internal feature registry has them tagged as an ES2020 feature.

## Expected Behavior

- When the transformer evaluates a target browser or runtime to determine which syntax transformations are needed, the compound logical assignment operators should be treated as an ES2021 feature
- For a target that supports ES2019 and ES2020 features but not full ES2021 support, the transformer should recognize that logical assignment operators require transformation (i.e., the flag should be enabled, not disabled)
- Other feature flags (ES2019 optional catch binding, ES2020 nullish coalescing, ES2022 class static block) should continue to be evaluated correctly

## Why This Matters

Because the feature is incorrectly classified under the wrong ECMAScript year, the transformer consults the wrong browser/engine support data when deciding whether to downcompile this syntax. As a result, it may skip the transformation for environments that genuinely cannot run this syntax natively, leading to broken output in older runtimes. Fixing the year classification ensures the correct compatibility data is used and the transformation is applied when needed.
