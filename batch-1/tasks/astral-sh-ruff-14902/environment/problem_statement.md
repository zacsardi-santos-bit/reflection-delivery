## Description

The existing lint rule for detecting invalid suffix arguments passed to a path's suffix-replacement method only catches cases where the suffix string is missing a leading dot. However, passing a lone dot as the suffix is also invalid at runtime — Python's path library will raise a runtime error in this case. The rule currently misses this scenario because it treats any string beginning with a dot as valid, even when that string is only the dot character by itself.

## Expected Behavior

- The rule should flag calls to the path suffix-replacement method where the argument is just a lone dot, for all supported path types
- The error message for the lone-dot case should reflect that the suffix is generically invalid (not just dotless)
- Unlike the dotless-suffix case, no automatic fix should be offered for the lone-dot case, because the correct fix is ambiguous — the user may need to remove the argument, extend it to a valid extension, or rethink the call entirely
- The rule name should be updated to reflect its broader scope, since it now catches more than just the missing-leading-dot case

## Why This Matters

Users who accidentally pass a lone dot as a path suffix will get a runtime crash rather than a lint warning. Catching this statically — at lint time — prevents a subtle class of bugs that only appear at runtime. The fix behavior distinction is intentional: while a dotless suffix has a clear correction (add the missing dot), a lone-dot suffix requires a judgment call from the developer, so no automatic fix should be suggested.
