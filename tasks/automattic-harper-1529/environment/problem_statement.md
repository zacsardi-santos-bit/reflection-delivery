## Description

Harper's phrase correction system handles common writing mistakes by mapping incorrect phrasings to their correct equivalents. Currently, related corrections that share the same underlying pattern are each registered as separate, independent rules. For example, correcting the error of using the wrong verb after an auxiliary — such as writing "have went" when "have gone" is correct — requires a separate rule for each auxiliary verb form (have/had/having/has). This leads to a large number of fragmented rules when a single grouped rule would be cleaner and more maintainable.

Additionally, some phrase corrections need to offer multiple alternative suggestions, and the current system doesn't express these as naturally grouped sets.

## Expected Behavior

- A new, dedicated correction module should group related phrase corrections together. Rules that share the same pattern but differ only in verb conjugation or inflection (like all forms of "have/had/having/has went") should be expressed as a single grouped rule rather than multiple independent ones.
- The new module should cover a wide range of common phrase errors including: confusing "adieu" with "ado", confusing "definitive" with "definite" in the phrase "definite article", confusing "explanation" with "exclamation", incorrectly using "hone in on" instead of "home in on", misusing "into" after invest, various "worse/worst" confusions, and others.
- For some corrections, multiple valid alternatives should be offered (e.g., correcting "how it looks like" could yield either "how it looks" or "what it looks like").
- The corrected forms must match the case of the original text.

## Why This Matters

Having all these related corrections in one cohesive module makes the linter easier to maintain and extend. Users benefit from consistent, accurate suggestions for all variants of common phrase errors rather than only the specific forms that happen to have their own individual rule.
