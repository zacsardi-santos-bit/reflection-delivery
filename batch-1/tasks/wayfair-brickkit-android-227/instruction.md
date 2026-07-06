Implement an abstract base class in Kotlin for the BrickKit framework that supports an observer pattern for change notifications. Ensure that the class allows components to register and unregister as listeners, and that it provides a default readiness state.

*   Implement the `DataModel` class as an abstract class in the file path `BrickKit/bricks/src/main/java/com/wayfair/brickkit/brick/DataModel.kt` within the package `com.wayfair.brickkit.brick`.
    *   Ensure the class can be instantiated via anonymous subclassing.
*   Define an open method `isReady()` in `DataModel` that returns `true` by default.
    *   Allow subclasses to override this method.
*   Implement the method `addUpdateListener(updateListener: DataModel.DataModelUpdateListener)` in `DataModel`.
    *   This method should register the provided listener to receive change notifications.
*   Implement the method `removeUpdateListener(updateListener: DataModel.DataModelUpdateListener)` in `DataModel`.
    *   This method should unregister the provided listener, preventing it from receiving further notifications.
*   Implement the method `notifyChange()` in `DataModel`.
    *   This method should call `notifyChange()` on every currently registered listener.
    *   Ensure that listeners removed via `removeUpdateListener` do not receive notifications.
*   Define a nested interface `DataModel.DataModelUpdateListener` within `DataModel`.
    *   The interface must include a single method `notifyChange()` that implementations must override.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.