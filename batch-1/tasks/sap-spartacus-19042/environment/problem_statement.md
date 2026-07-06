## Description

In our NgRx-based Spartacus application, action classes that represent failure states are expected to implement a specific error-handling interface. This is a convention that enables consistent error handling across the codebase. However, there is currently no automated enforcement of this contract — developers can easily create or modify failure action classes without adding the required interface, and the code will still compile without any warning.

## Expected Behavior

- A custom lint rule should flag any action class whose name indicates it represents a failure (by containing "Fail") but that does not implement the required error interface.
- The lint rule should be able to automatically fix violations by both adding the required interface to the class declaration and inserting the missing import statement.
- Reusable utility functions should be provided for: (1) checking whether a class already implements a given interface, and (2) adding a missing interface to a class declaration with correct placement — whether the class has no existing interfaces, already implements other interfaces, or extends a superclass. 
- Similarly, reusable utility functions should be provided for: (1) checking whether a given identifier is already imported from a specific module, and (2) inserting a missing import statement, either at the top of the file or after the last existing import.

## Why This Matters

Without this enforcement, failure action classes silently skip implementing the required error contract, making it impossible for the error handling infrastructure to recognize them consistently. Automating this check with an auto-fixable lint rule prevents regressions and makes it easy to bring the entire codebase into compliance.
