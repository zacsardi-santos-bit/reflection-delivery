I'm running into a problem with the secure model loading utilities in PaddlePaddle.

*   The _is_safe_class function must return True for user-defined classes (instances of type) that do not define any of __reduce__, __reduce_ex__, __getstate__, or __setstate__ in their own class dictionary, including regular classes, dataclasses, and classes with regular methods such as classmethods and staticmethods.

*   The _is_safe_class function must return False for built-in functions (such as len, print, range), built-in methods (such as list.append, dict.keys), and module objects.

*   The _is_safe_class function must return False for non-class values including strings, integers, lists, and dicts.

*   The _is_safe_class function must return False for any class that defines __reduce__, __reduce_ex__, __getstate__, or __setstate__ directly in its own class dictionary.

*   The safe_load_pickle function must successfully deserialize user-defined classes (including dataclasses) that do not define dangerous serialization methods, preserving all their attribute values.

*   The safe_load_pickle function must successfully deserialize dict and OrderedDict objects whose values include safe user-defined classes, preserving all field values.

*   When safe_load_pickle encounters a class with __reduce__ (or other dangerous serialization methods), it must raise a pickle.UnpicklingError and the error message must contain the string '__reduce__'.

*   When safe_load_pickle encounters a container (such as a dict) that mixes safe and unsafe classes, it must raise a pickle.UnpicklingError.

*   The _parse_every_object function must return a dataclass instance unchanged when that instance is the top-level object, without applying condition_func or convert_func to it.

*   The _parse_every_object function must pass dataclass instances through unchanged when they appear as elements inside lists, tuples, dicts, or OrderedDicts, while still applying condition_func and convert_func to non-dataclass elements in those containers.


*   Interface details: Type: Function
Name: _is_safe_class
Location: python/paddle/framework/restricted_unpickler.py
Signature: _is_safe_class(cls) -> bool
Description: Checks whether a class is safe to deserialize. Returns True if cls is a user-defined class (an instance of `type`) that does not define any of the dangerous serialization methods (__reduce__, __reduce_ex__, __getstate__, __setstate__) in its own __dict__. Returns False for built-in functions (types.BuiltinFunctionType), built-in methods (types.BuiltinMethodType), module objects (types.ModuleType), and any non-class values such as strings, integers, lists, or dicts. Returns True for regular user-defined classes, dataclasses, and classes with regular methods (including classmethods and staticmethods) as long as none of the dangerous methods are present in the class's own __dict__.

Type: Function
Name: safe_load_pickle
Location: python/paddle/framework/restricted_unpickler.py
Signature: safe_load_pickle(file) -> object
Description: Existing function whose behavior is extended. When a class is encountered during deserialization that is not in the built-in whitelist, the loader now also accepts the class if _is_safe_class returns True for it. If the class fails the _is_safe_class check (e.g., has __reduce__), a pickle.UnpicklingError is raised and the error message must contain the string "__reduce__".

Type: Function
Name: _parse_every_object
Location: python/paddle/framework/io.py
Signature: _parse_every_object(obj, condition_func, convert_func) -> object
Description: Existing function whose behavior is extended. When obj is a dataclass instance, it must be returned as-is without further traversal or transformation, even if the object is iterable. This applies both when the dataclass is the top-level object and when it appears as a value inside a dict, list, tuple, or OrderedDict — in those container cases the dataclass elements are passed through unchanged while non-dataclass elements continue to be processed normally.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.