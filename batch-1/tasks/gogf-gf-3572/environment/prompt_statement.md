I'm running into a deadlock when I try to remove elements from a thread-safe map or set while iterating over it. I'm using the safe/concurrent variants of the map and set container types, and inside the iteration callback I call the remove method on the same container. The program just hangs forever instead of completing the iteration and correctly reflecting the removed elements.

This is a really common pattern — I want to filter out entries in a single pass without having to collect keys first and then delete in a second step. It seems like the iteration is holding some kind of lock, and then the remove operation inside the callback tries to acquire the same lock, causing a deadlock.

The fix should allow calling remove from within the iteration callback on any thread-safe map or set type. After the iteration finishes, the collection should correctly show only the elements that weren't removed during the traversal.
