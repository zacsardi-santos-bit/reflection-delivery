Implement language support for Faroese and Norwegian Nynorsk in spaCy, ensuring proper tokenization for these languages. Create language classes and define tokenizer exceptions to handle specific linguistic features such as abbreviations, decimal numbers, and punctuation.

*   Add Faroese language support:
    *   Implement the `Faroese` class in `spacy/lang/fo/__init__.py` extending `Language`.
        *   Set `lang` attribute to "fo".
    *   Implement `FaroeseDefaults` class in `spacy/lang/fo/__init__.py` extending `BaseDefaults`.
        *   Include `tokenizer_exceptions`, `infixes`, `suffixes`, and `prefixes`.
    *   Define `TOKENIZER_EXCEPTIONS` in `spacy/lang/fo/tokenizer_exceptions.py`.
        *   Include exceptions for common abbreviations (e.g., "v.m.", "m.a.") and month abbreviations.
    *   Ensure the tokenizer:
        *   Handles abbreviations like "v.m." as single tokens.
        *   Treats decimal numbers with commas (e.g., "2,7") as single tokens.
        *   Separates punctuation from words.

*   Add Norwegian Nynorsk language support:
    *   Implement the `NorwegianNynorsk` class in `spacy/lang/nn/__init__.py` extending `Language`.
        *   Set `lang` attribute to "nn".
    *   Implement `NorwegianNynorskDefaults` class in `spacy/lang/nn/__init__.py` extending `BaseDefaults`.
        *   Include `tokenizer_exceptions`, `prefixes`, `infixes`, `suffixes`, and `syntax_iterators`.
    *   Define `TOKENIZER_EXCEPTIONS` in `spacy/lang/nn/tokenizer_exceptions.py`.
        *   Include exceptions for abbreviations and month abbreviations with normalized forms (e.g., "jan." -> "januar").
        *   Handle date patterns ("1." through "31.") and common abbreviations.
    *   Define `TOKENIZER_PREFIXES`, `TOKENIZER_INFIXES`, and `TOKENIZER_SUFFIXES` in `spacy/lang/nn/punctuation.py`.
    *   Ensure the tokenizer:
        *   Handles abbreviations like "nov." as single tokens.
        *   Treats numbers with periods as thousands separators (e.g., "8.848") as single tokens.
        *   Treats decimal numbers with commas (e.g., "7,8") as single tokens.
        *   Handles date formats with numbers followed by periods as single tokens.
        *   Separates punctuation from words.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.