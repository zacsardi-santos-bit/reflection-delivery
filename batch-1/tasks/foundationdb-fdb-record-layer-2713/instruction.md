Implement a new specialized analyzer for Lucene-based auto-complete search that supports CJK (Chinese, Japanese, Korean) text, email addresses, and mixed-script content. Ensure the analyzer handles tokenization, case-insensitive matching, and configurable minimum token length filtering.

Requirements:

*   Implement `EmailCjkSynonymAnalyzer` in the `com.apple.foundationdb.record.lucene` package.
    *   Constructor: `EmailCjkSynonymAnalyzer(CharArraySet stopwords, int minTokenLength, int minAlphanumericTokenLength, int maxTokenLength, boolean withEmailTokenizer, boolean withSynonymGraphFilter, SynonymMap synonymMap)`.
    *   Declare `public static final String UNIQUE_IDENTIFIER = "SYNONYM_EMAIL"`.
    *   Tokenize CJK text character-by-character.
    *   Use email-aware tokenization when `withEmailTokenizer` is true, recognizing email patterns and treating adjacent Hangul and Latin characters as a single token without space boundaries.
    *   Apply alphanumeric minimum-length filtering: discard tokens shorter than `minAlphanumericTokenLength`.
    *   Apply lowercasing and ASCII folding for case-insensitive matching.
    *   Filter stop words using the provided `CharArraySet`.
    *   Apply synonym expansion when `withSynonymGraphFilter` is true and `synonymMap` is non-null.

*   Implement `EmailCjkSynonymAnalyzerFactory` in the `com.apple.foundationdb.record.lucene` package.
    *   Implement `LuceneAnalyzerFactory` and register via `@AutoService`.
    *   Declare `public static final CharArraySet MINIMAL_STOP_WORDS` for use in tests.
    *   Implement `getName()` to return `ANALYZER_FACTORY_NAME` ("SYNONYM_EMAIL").
    *   Implement `getType()` to return `LuceneAnalyzerType.FULL_TEXT`.
    *   Implement `getIndexAnalyzerChooser(Index)` and `getQueryAnalyzerChooser(Index, AnalyzerChooser)` to provide analyzer choosers.

*   Ensure the analyzer is discoverable as a service provider so that an index configured with `LUCENE_ANALYZER_NAME_OPTION` set to `EmailCjkSynonymAnalyzer.UNIQUE_IDENTIFIER` uses this factory.
*   When parsing a phrase query with `minAlphanumericTokenLength=3`, exclude short words from the completed-token list. For example, the query "United States of Ameri" should produce completed tokens `['united', 'states']` and prefix `'ameri'`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.