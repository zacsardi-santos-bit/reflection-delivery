I'm working with the ferret scripting engine and I need a way to merge multiple objects together into a single unified object within my queries. Right now there's no built-in function in the objects standard library that does this, so I'm stuck manually working around it.

What I need is a function that can take several objects — either passed individually or collected in a list — and combine all their properties into one new object. If the same key appears in more than one source object, the last source should win. The result should be fully independent of the originals, so modifying a source object afterward doesn't change the merged output.

It should also handle edge cases gracefully: returning an error if no arguments are provided, if any argument isn't an object, if a list contains non-object items, or if more than one list is passed. Passing an empty list should simply return an empty object.
