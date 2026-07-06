Implement a new smart pointer type, `DynGd<T, D>`, that combines Godot's smart pointer system with Rust's trait dynamic dispatch. This will allow you to hold multiple Godot objects of different classes under a shared Rust trait while maintaining access to Godot engine methods and safe borrow checking.

*   Introduce `DynGd<T, D>` where `T` is a Godot class and `D` is a trait object.
    *   Ensure `DynGd`, `DynGdRef`, `DynGdMut`, `AsDyn`, and `godot_dyn` are accessible via `godot::prelude`.
*   Implement `Gd<T>::into_dyn<D>()` to convert `Gd<T>` into `DynGd<T, D>`.
    *   Allow type inference for `D` when `T` has a single `AsDyn` relation.
    *   Require `T` to implement `AsDyn<D>`.
*   Implement `dyn_bind()` and `dyn_bind_mut()` methods for `DynGd`.
    *   `dyn_bind()` returns a `DynGdRef` shared guard.
    *   `dyn_bind_mut()` returns a `DynGdMut` exclusive guard.
    *   Ensure `DynGdRef` implements `Deref<Target=D>` and `DynGdMut` implements both `Deref<Target=D>` and `DerefMut<Target=D>`.
    *   Support deref-coercion for `DynGdMut`.
*   Manage guard acquisition and object freeing:
    *   Allow multiple `DynGdRef` guards simultaneously.
    *   Panic on acquiring `DynGdMut` if any guard is held.
    *   Panic on acquiring `DynGdRef` if a `DynGdMut` guard is held.
    *   Panic on `free()` if any guard is held.
    *   Allow `free()` and guard acquisition after all guards are dropped.
*   Implement `upcast<Base>()` for `DynGd`.
    *   Return `DynGd<Base, D>` where `Base` is a Godot parent class.
    *   Maintain the same `instance_id` before and after upcasting.
    *   Ensure trait dispatch remains correct post-upcasting.
*   Implement `into_gd()` for `DynGd` to return `Gd<T>`.
    *   Ensure the returned `Gd<T>` has the same `instance_id`.
*   Implement `Clone` for `DynGd`.
    *   Ensure all clones share the same underlying Godot object and runtime borrow state.
*   Implement `Deref<Target=Gd<T>>` and `DerefMut<Target=Gd<T>>` for `DynGd`.
    *   Allow direct calling of Godot class methods on `DynGd`.
*   Implement `AsObjectArg` for `&DynGd<U, D>`.
    *   Allow passing `DynGd` references to Godot API functions accepting a parent class type.
*   Implement the `#[godot_dyn]` attribute macro.
    *   Apply to `impl Trait for Class` blocks where `Class` is a Godot class.
    *   Generate `AsDyn<dyn Trait>` implementation with `dyn_upcast` and `dyn_upcast_mut` methods.
    *   Do not support generic parameters or inherent impl blocks.
*   Define the `AsDyn` trait with `dyn_upcast` and `dyn_upcast_mut` methods.
    *   Allow coercion of implementing class to the trait object.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.