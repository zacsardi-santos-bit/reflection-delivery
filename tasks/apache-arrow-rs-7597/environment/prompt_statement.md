I'm trying to use the batch coalescing functionality from the arrow-select library in my own Rust code. The coalescer is supposed to let me push small record batches incrementally, flush any remaining buffered data, and then retrieve merged output batches of a target size. But when I try to import it, I get a compile error saying the module can't be found — it seems like the coalescing module is internal-only and not exposed as part of the public API.

I'd like this to be a proper public API so I can create a coalescer with a given schema and target batch size, push record batches into it, call a method to finalize any in-progress buffer, and then retrieve the completed batches one by one. After pushing some batches and flushing the buffer, I'd expect to get back a completed batch with the correct row count.

Could you make the batch coalescing functionality publicly accessible from outside the crate?
