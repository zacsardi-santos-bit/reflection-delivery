I'm working on a Godot extension in Rust and I need a way to hold multiple Godot objects of different classes under a shared Rust trait — essentially combining Godot's smart pointer system with Rust's trait dynamic dispatch. Right now I can either use a Godot smart pointer (which gives me access to engine methods, upcasting, and safe borrow checking) or a Rust trait object (which gives me polymorphic method dispatch), but there's no way to have both at the same time.

What I want is a new smart pointer type that wraps a Godot object and additionally carries a trait relationship. I should be able to acquire shared or exclusive access guards that let me call trait methods, while also being able to call Godot engine methods directly through the pointer. The same runtime borrow-checking rules that apply to the existing Godot pointer should apply here too — for example, holding a mutable guard should prevent other clones from acquiring any guard or freeing the object.

I also want to be able to upcast the pointer to a parent Godot class while keeping the trait connection intact, and to convert back to the original concrete pointer type when I need to. Passing the new pointer to Godot API functions that accept a parent class type should just work.

To register the trait relationship, I'd like an attribute macro that I can put on a trait implementation block for a Godot class and have the necessary connection auto-generated for me.

All of these new types and the attribute should be accessible from the standard prelude so users don't need extra imports.
