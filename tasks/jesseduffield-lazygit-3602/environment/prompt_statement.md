I'm working on improving the "find base commit for fixup" feature in lazygit. Right now, it only works when the current diff has deleted lines — it uses those deleted lines to figure out which earlier commit the changes belong to. But there's a real gap: if all of my changes are purely additive (like inserting a new line or adding a comment into code I added in an earlier commit), the feature just fails instead of trying to find the right base commit.

I'd like the feature to also handle the case where the entire diff consists only of added lines. In that scenario, it should look at the lines surrounding each insertion point to determine which commit those surrounding lines came from, and use that as the base commit.

There's also a related bug in the internal diff parsing logic: when a hunk has both deleted and added lines, it should be treated as a deletion hunk (and only the deleted line count should matter), but currently it seems to be getting misclassified. This misclassification affects how the feature decides which strategy to use.

The parsing logic needs to correctly separate hunks into two categories: those that contain deleted lines (regardless of whether they also have added lines), and those that contain only added lines. This separation drives which strategy is used to find the base commit.
