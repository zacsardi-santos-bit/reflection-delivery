I'm working with a blockchain library that already supports fetching transaction details, but I can't find any built-in way to fetch transaction receipts. After submitting a transaction, I need to check whether it succeeded, how much gas it used, and any events it emitted — all of which come from the receipt, not the transaction itself.

I'd like the library to have a proper action for retrieving a transaction receipt by hash, with optional chain targeting, following the same patterns as the existing transaction-fetching utilities. I also need the corresponding query cache utilities (a query key helper and a query options helper) so I can integrate with the standard caching patterns used elsewhere in the library.

On the React side, I need a hook that wraps this action with the library's reactive query system. The hook should stay idle and not make any network requests when no transaction hash has been provided yet — this is important because in many flows the hash isn't known until after a transaction is submitted. Once a hash is available, the hook should automatically fetch the receipt. The hook should also automatically include the active chain's identifier in its cache key, even if I don't pass one explicitly.

All of these new capabilities should be accessible through the library's standard public exports so they can be imported the same way as everything else.
