## Description

The Scala MongoDB driver has a usability problem with operations that complete without returning a meaningful value — for example, uploading files to GridFS. These operations currently use a Java void-type representation for their result, which is not idiomatic in Scala. Subscribers receive an empty, meaningless value as the emitted item, and these observables cannot be used in Scala for-comprehensions or chained with standard monadic operations.

## Expected Behavior

- When an operation completes successfully but produces no meaningful result value, an adapter should emit exactly **one** Scala unit value and then signal completion — making it behave like a single-element observable.
- If the underlying operation signals an error, the adapter should propagate that error and emit no items (not signal completion).
- These adapted observables should support explicit backpressure demand (i.e., subscribers that explicitly request one item).
- These adapted observables should compose naturally with Scala for-comprehensions, so multiple operations can be chained together using idiomatic Scala patterns.
- The same adaptation must be available for GridFS upload operations specifically, which have an additional interface constraint requiring delegation of file identifier accessors to the underlying publisher.
- The existing GridFS upload observable wrapper should work with the adapted unit-emitting type rather than the existing Java void-type representation.

## Why This Matters

Working with operations that return nothing in Java but should signal "done" in Scala is a common friction point for Scala developers using the driver. Without this adaptation, users must write boilerplate workarounds to chain or react to the completion of write operations. With this fix, those operations become first-class Scala observables that participate naturally in monadic composition.
