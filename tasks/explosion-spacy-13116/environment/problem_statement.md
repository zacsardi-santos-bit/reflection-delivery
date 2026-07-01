## Description

spaCy currently does not support Faroese (language code: `fo`) or Norwegian Nynorsk (language code: `nn`) languages. Users who need to process text in these Nordic languages cannot use spaCy's tokenization and NLP pipeline features.

Faroese is a North Germanic language spoken primarily in the Faroe Islands, while Norwegian Nynorsk is one of the two official written standards of the Norwegian language. Both languages have specific tokenization requirements, particularly around handling abbreviations, decimal numbers with commas, and thousands separators with periods.

## Expected Behavior

- `get_lang_class("fo")` should return a valid Faroese language class
- `get_lang_class("nn")` should return a valid Norwegian Nynorsk language class
- The Faroese tokenizer should:
  - Handle common Faroese abbreviations as single tokens (e.g., "v.m.", "m.a.", "nr.", month abbreviations)
  - Keep decimal numbers with commas together (e.g., "2,7" should be one token)
  - Properly separate punctuation from words
- The Norwegian Nynorsk tokenizer should:
  - Handle common Norwegian abbreviations as single tokens (e.g., "nov.", "f.eks.", "bl.a.")
  - Normalize month abbreviations (e.g., "jan." normalizes to "januar")
  - Keep numbers with periods as thousands separators together (e.g., "8.848")
  - Keep decimal numbers with commas together (e.g., "7,8")
  - Handle date numbers followed by periods (e.g., "1.", "15.", "31.")

## Current Behavior

Attempting to load Faroese or Norwegian Nynorsk language classes raises an error because these languages are not implemented in spaCy. Users cannot tokenize or process text in these languages.
