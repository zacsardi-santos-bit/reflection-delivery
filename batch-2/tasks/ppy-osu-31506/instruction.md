Implement a mechanism to ensure that the storyboard continues to receive update ticks from the engine even when it is fully dimmed and invisible. This will prevent stuttering when the dim level is reduced and the storyboard becomes visible again.

*   Update the `DrawableStoryboard` class in `osu.Game/Storyboards/Drawables/DrawableStoryboard.cs`:
    *   Expose the `AlwaysPresent` property to ensure the storyboard continues receiving `Update()` calls even when invisible.
    *   Ensure that the `AlwaysPresent` property is set to true when the storyboard show setting is enabled.

*   Modify the `DimmableStoryboard` class in `osu.Game/Screens/Play/DimmableStoryboard.cs`:
    *   Set `AlwaysPresent` to true on both the `Content` container and the inner `DrawableStoryboard` instance whenever the storyboard show setting is enabled.
    *   Ensure that the storyboard's child drawables continue to receive `Update()` calls even when the storyboard is fully dimmed to invisible (dim level = 1.0).

*   Ensure that the storyboard's internal update loop continues to run regardless of its visibility, maintaining synchronization with the game clock.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.