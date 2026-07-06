## Description

The linter does not currently catch a common English idiom error where writers use "mute point" instead of the correct phrase "moot point". These two phrases are easily confused because they sound similar when spoken aloud, making this a frequent written mistake.

## Expected Behavior

- When a writer uses the phrase "mute point" in their text, the linter should flag it as incorrect.
- The linter should suggest replacing "mute point" with "moot point".
- Applying the suggestion should result in the corrected phrase "moot point" appearing in the text.

## Why This Matters

The linter already handles many similar phrase confusions and idiom errors. Adding detection for "mute point" → "moot point" is a natural extension of this capability and will help writers avoid a mistake that often goes unnoticed because both phrases are real English words that pass spelling checks.
