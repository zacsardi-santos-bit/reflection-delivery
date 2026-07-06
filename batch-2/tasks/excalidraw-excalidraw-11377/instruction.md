I've found two bugs in the Excalidraw codebase that need to be fixed.

*   AnimationController.start(key, callback) must invoke the callback synchronously on the first frame immediately upon being called.

*   When the callback passed to AnimationController.start returns { keep: true }, the animation continues and the next frame is scheduled.

*   When the callback passed to AnimationController.start returns null, the animation stops and the key is removed from the set of running animations.

*   AnimationController.running(key) must return false after an animation associated with that key has naturally ended (callback returned null) or been cancelled.

*   AnimationController.cancel(key) must stop a running animation; after cancellation a new animation can be successfully started with a different key.

*   When all animations have stopped or been cancelled and no frames are pending, there must be no remaining scheduled timers.

*   When updateScene is called with a collaborators map containing an entry whose pointer has tool set to 'laser' and button set to 'down', a path element must appear in the SVG layer of the canvas.

*   When updateScene is called with an empty collaborators map (all collaborators removed), all laser trail path elements previously rendered for remote collaborators must be removed from the SVG layer.


*   Interface details: Type: Class
Name: AnimationController
Location: packages/excalidraw/renderer/animation.ts
Description: Static class that manages multiple named animations. Provides methods to start, cancel, and query animations by string key. Each animation runs frame by frame via a callback that controls continuation.
Signature:
  static start(key: string, callback: (frame: { state: any }) => { keep: true } | null): void
  static cancel(key: string): void
  static running(key: string): boolean


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.