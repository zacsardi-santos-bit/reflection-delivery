Implement a group shape type in the Svelte/TypeScript port of MonoSketch to manage collections of child shapes. Ensure the rectangle shape type is publicly accessible and can be instantiated without an explicit id. Provide a factory method for creating rectangles from plain objects.

*   Export the `Group` class from `monosketch-svelte/src/lib/mono/shape/shape/group.ts`.
    *   Extend `AbstractShape`.
    *   Constructor signature: `constructor(id: string | null = null, parentId: string | null = null)`.
    *   Implement `itemCount` as a getter returning the number of shapes in the group.
    *   Implement `items` as an ordered iterable of all shapes in the group.
    *   Implement `add(shape: AbstractShape, position: AddPosition = AddPosition.Last): void`.
        *   Reject shapes with a non-null `parentId` not matching the group's id.
        *   Accept shapes with a null `parentId` or matching the group's id, updating the shape's `parentId` and increasing `itemCount`.
        *   Ensure idempotency: adding a shape already in the group does not change `itemCount` or `items`.
        *   Support `AddPosition` parameter:
            *   `AddPosition.First` inserts at the beginning.
            *   `AddPosition.After(referenceShape)` inserts after the reference shape.
            *   Default `AddPosition.Last` inserts at the end.
    *   Implement `remove(shape: AbstractShape): void`.
        *   Remove the specified shape, decreasing `itemCount` and removing it from `items`.
    *   Implement `changeOrder(shape: AbstractShape, moveActionType: MoveActionType): void`.
        *   `MoveActionType.UP` moves the shape one position toward the end.
        *   `MoveActionType.DOWN` moves the shape one position toward the beginning.
        *   `MoveActionType.TOP` moves the shape to the last position.
        *   `MoveActionType.BOTTOM` moves the shape to the first position.

*   Export the `Rectangle` class from `monosketch-svelte/src/lib/mono/shape/shape/rectangle.ts`.
    *   Constructor signature: `constructor(rect: Rect, id: string | null = null, parentId: string | null = null)`.
    *   Default the `id` parameter to null.
    *   Implement a static `fromRect` method:
        *   Accept an object with `rect` (required), `id` (optional, default null), and `parentId` (optional, default null).
        *   Return a `Rectangle` instance.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.