I want to add a new lint rule to the nursery group that flags the use of two legacy string substring methods that should no longer be used in modern JavaScript. One of these methods takes a length as its second argument instead of an end index, making it confusing compared to the modern alternative. The other has surprising behavior around negative arguments and argument order.

The rule should report a warning whenever either of these legacy methods is accessed or called on any object, including through optional chaining. The warning should tell the developer to use the modern equivalent method instead, explain that it is more commonly used and less surprising, and link to MDN for more details.

For calls that have no arguments, the rule should offer an automatic unsafe fix that simply renames the method. For calls with arguments, no automatic fix should be offered since the argument semantics are different and require the developer to manually migrate the code. A bare property access of one of these method names (without actually calling it) should also be flagged, but without any fix.

Calls that already use the modern alternative should not be flagged at all.
