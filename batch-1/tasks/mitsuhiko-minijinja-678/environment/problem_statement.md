## Description

When writing a recursive for loop to render a tree structure (like a navigation menu), it's often necessary to nest another loop inside the outer recursive loop — for example, to iterate over styling variants for each item. The problem is that inside the inner loop, the recursive callable is no longer accessible under its usual name, because the inner loop's own context overwrites it. This makes it impossible to recurse into children from within the nested loop without restructuring the template significantly.

## Expected Behavior

- Inside a recursive for loop, it should be possible to save a reference to the loop's recursive callable by assigning it to another variable before entering the nested loop.
- That saved reference should remain callable from inside the nested loop, successfully recursing into child items.
- If the recursive callable is used as a function call outside of any recursive loop context, the template engine should report a clear error indicating the function is not available, rather than silently failing or producing confusing output.

## Why This Matters

This pattern is common in navigation menus, tree renderers, and other hierarchical template structures. Without the ability to alias the recursive callable, template authors must either avoid nested loops entirely or duplicate logic. The improved error message also helps developers quickly diagnose mistakes when they accidentally call the recursive callable in the wrong context.
