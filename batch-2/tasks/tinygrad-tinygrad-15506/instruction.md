I'm working on tinygrad's device management and I'd like to simplify how the default device is handled.

*   Device.DEFAULT must support direct assignment as a class-level attribute (e.g., Device.DEFAULT = 'cpu' must succeed without raising an AttributeError or similar).

*   Device.canonicalize(None) must return the uppercase canonical device name derived from Device.DEFAULT, even when Device.DEFAULT has been set to a lowercase value.

*   When a program accesses Device.DEFAULT in an environment that has an old-style device env variable (e.g., CPU=1) set but does NOT have a DEV variable explicitly set, the program must exit with a non-zero return code and emit a message containing the word 'deprecated' to stderr.

*   The DEV context variable is no longer required as a mechanism for temporarily switching the default device; code relying on Context(DEV=...) to change the active device is no longer supported.


*   Interface details: Type: Class Attribute
Name: Device.DEFAULT
Location: tinygrad/device.py
Signature: Device.DEFAULT (readable and writable string attribute)
Description: The default device name. Must support direct assignment (Device.DEFAULT = "some_device"). When read, returns the current default device name. When Device.canonicalize(None) is called, it must return the uppercased form of whatever Device.DEFAULT is currently set to.

Type: Method
Name: Device.canonicalize
Location: tinygrad/device.py
Signature: Device.canonicalize(device: Optional[str]) -> str
Description: Returns the canonical (uppercased) device name. When called with None, returns the uppercased form of Device.DEFAULT. This method must work correctly after Device.DEFAULT has been directly assigned a lowercase device name.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.