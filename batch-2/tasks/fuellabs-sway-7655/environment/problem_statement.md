## Description

The Sway compiler's optimizer includes a Common Subexpression Elimination (CSE) pass that identifies and removes redundant computations in the intermediate representation. However, the CSE pass currently fails to recognize a whole class of obviously redundant instructions: multiple loads of the address of the same local variable, global variable, configurable constant, or storage key.

Since the address of any such entity is invariant within a function — it never changes between program points — any two instructions loading the same address are always guaranteed to produce identical values. The CSE pass should be able to recognize this and replace references to the redundant second load with the first, allowing the dead second load to later be removed by the Dead Code Elimination pass.

## Expected Behavior

- When two instructions both load the address of the same local variable, they should be treated as congruent by CSE, and uses of the second should be rewritten to use the first.
- This congruence should extend to instructions loading the address of global variables, configurables, and storage keys.
- As a side effect, instructions that depend on the redundant load (such as pointer-arithmetic or element-access instructions that used it as a base) are automatically updated to use the surviving instruction.

## Why This Matters

Currently, the compiler generates slightly larger and less efficient bytecode than necessary because these redundant address loads are not eliminated. Fixing this produces smaller programs and reduced gas consumption, as demonstrated by the measurable bytecode size reductions in various compiled programs.
