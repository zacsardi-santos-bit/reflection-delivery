I'm running into a bundling bug when using multiple entry points with entry signatures disabled. In my project, some entry modules are also imported dynamically by other modules. When I configure the bundler to not preserve entry signatures, it incorrectly strips the exports from entries that are dynamically imported. This means that at runtime, the dynamic import can't access the expected values from that entry — the exports are simply missing from the output chunk.

The expected behavior is that if an entry module is a target of a dynamic import, it should still have its exports present in the output, since the dynamic importer depends on them. Only entries that are purely static (never dynamically imported) should have their exports suppressed under this mode.

Additionally, when multiple entry points reference the same underlying source module, the shared content should be extracted into a single deduplicated chunk rather than being duplicated across entries.

Could you fix the bundler so that entry modules that serve as dynamic import targets correctly retain their exports in the output, even when entry signature preservation is disabled?
