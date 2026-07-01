I'm trying to use Presidio's anonymizer with a custom operator that I've written myself. Right now there's no way to register my custom operator with the engine at runtime — I can only use the built-in ones. I'd like to be able to add my own operator class to the engine so it can be used by name just like the built-in operators. Similarly, I'd like to be able to remove operators I don't need.

I also ran into an issue where all engine instances seem to share the same operator registry at the class level, which means adding an operator to one engine affects all other instances too. Each engine instance should have its own independent registry.

Additionally, when writing a custom operator, I need to know the entity type being processed (e.g., "PERSON", "LOCATION") so I can implement entity-specific logic. Currently, the entity type isn't reliably available in the operator's parameters during both the validation step and the operation step.

Could you add runtime support for registering and removing custom operators on both the anonymizer and deanonymizer engines, with per-instance state and proper entity type injection into operator parameters?
