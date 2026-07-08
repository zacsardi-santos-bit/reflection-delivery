I'm cleaning up our project classification page and hitting a wall because it's still in an old file format that our test tooling just can't touch. The thing can't be imported or rendered in tests since it doesn't do a default export the way our standard JS setup expects, and honestly it's slowing down anything new we want to build on top of it.

What I want is to migrate that classification page over to a plain JavaScript module, the normal kind, so it exports the component as the default export. Once it's importable like everything else in the codebase we can actually unit test it and catch regressions instead of guessing.

Behavior needs to stay correct after the move though. When I render it with a project (and no logged-in user, that case matters) it should still show its main classification container fine. And the "project finished" banner is the fiddly bit, it should only pop up when the project's actually been marked complete, so never when the project is still active and never when there's no completion state passed in at all. Basically if the completion flag isn't set, that banner stays hidden, no exceptions.

So yeah, convert it to the standard module format, default export, keep the rendering logic intact. Thanks!
