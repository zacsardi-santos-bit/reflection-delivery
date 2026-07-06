I'm working with the external corpus loading feature in our speculative decoding system and I've found a few problems that need to be fixed together.

*   NgramCorpus.load_external_corpus_named must return an integer representing the number of tokens loaded across all provided chunks. This return value is intended to be passed to commit_external_corpus_load.

*   NgramCorpus.load_external_corpus_named must raise a ValueError whose message contains the substring 'already exists' when called with a corpus_id that has already been committed (i.e., previously loaded and committed via commit_external_corpus_load and not yet removed).

*   When NgramCorpus.load_external_corpus_named raises any exception, all previously committed corpora must remain fully intact — they must still appear in list_external_corpora() and must still produce correct matching results.

*   NgramCorpus must expose a new method commit_external_corpus_load(corpus_id: str, loaded_token_count: int) -> None that records the number of tokens contributed by the named corpus and adds that count to the internal total, thereby updating the token budget.

*   NgramCorpus must expose a new read-only property remaining_token_budget that returns an integer equal to external_corpus_max_tokens minus the sum of token counts for all currently committed corpora.

*   When remove_external_corpus is called for a corpus that was previously committed, remaining_token_budget must increase by the token count that was recorded for that corpus at commit time.

*   The two-phase load pattern must be used: call load_external_corpus_named to stage and build the corpus (returns token count), then call commit_external_corpus_load with the corpus_id and the returned token count to finalize budget tracking. The corpus is not tracked in the budget until commit_external_corpus_load is called.


*   Interface details: Type: Class
Name: NgramCorpus
Location: python/sglang/srt/speculative/cpp_ngram/ngram_corpus.py
Description: Manages the ngram corpus used for speculative decoding. Requires new method and property for two-phase corpus loading with token budget tracking.

Signature: commit_external_corpus_load(corpus_id: str, loaded_token_count: int) -> None
Description: Commits the bookkeeping for a successfully staged corpus load. Must be called after load_external_corpus_named returns successfully, passing the token count returned by that call. Updates internal per-corpus token count and total loaded token counter.

Signature: remaining_token_budget -> int (read-only property)
Description: Returns the number of tokens still available for loading new external corpora. Computed as external_corpus_max_tokens minus the sum of all committed corpus token counts. Increases when remove_external_corpus is called; decreases when commit_external_corpus_load is called.

Signature: load_external_corpus_named(corpus_id: str, chunks: Iterable[Sequence[int]]) -> int
Description: Existing method — behavior is updated. Must now return an integer (the number of tokens loaded across all chunks). Must raise ValueError with a message containing "already exists" if corpus_id has already been committed (i.e., it appears in a previously successful load+commit pair). On any exception during load, must not modify the state of previously committed corpora.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.