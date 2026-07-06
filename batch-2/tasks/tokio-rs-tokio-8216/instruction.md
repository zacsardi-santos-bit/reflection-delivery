I'm working with a stream map that holds multiple named streams, and I've run into a panic when calling the method that returns a combined size estimate for all the streams it contains.

*   When StreamMap::size_hint() aggregates the lower bounds of contained streams, it must use saturating addition so that summing values that would exceed usize::MAX saturates to usize::MAX rather than overflowing or panicking.

*   When StreamMap::size_hint() aggregates the upper bounds of contained streams, it must use checked addition: if the sum of two Some(a) + Some(b) values would overflow, the upper bound must become None rather than panicking.

*   A StreamMap containing two streams that each report a size hint of (usize::MAX, Some(usize::MAX)) must return (usize::MAX, None) from its own size_hint() method — not panic.


*   Interface details: Type: Method
Name: size_hint
Location: tokio-stream/src/stream_map.rs
Signature: fn size_hint(&self) -> (usize, Option<usize>)
Description: Returns the combined size hint of all streams contained in the StreamMap. This method is implemented as part of the Stream trait impl for StreamMap. The lower bound must be accumulated with saturating addition; the upper bound must be accumulated with checked addition, yielding None when overflow would occur.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.