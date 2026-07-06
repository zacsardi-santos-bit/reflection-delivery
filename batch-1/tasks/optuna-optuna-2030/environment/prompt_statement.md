I'm working on adding multi-objective optimization support to the relational database storage backend. Right now, the schema stores a single optimization direction directly on the study record and a single objective value per trial, which makes it impossible to handle studies with more than one objective.

I need to refactor the schema so that optimization directions are stored in their own table — one row per objective per study, where each row tracks an objective index and the corresponding direction. Similarly, trial objective values should be stored in a separate table with one row per objective per trial.

Both new tables need proper lookup methods (find by study/trial and objective index, or list all for a given study/trial), and cascade deletion should work so that removing a study cleans up its direction rows, and removing a trial cleans up its value rows (both final objective values and intermediate values recorded during the trial).

The existing way of constructing a study with a single direction field needs to be replaced with a relationship to the new direction records. This schema change also requires registering a new migration version in the storage layer's version history.
