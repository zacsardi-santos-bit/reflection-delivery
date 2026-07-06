I'm working with Active Job and I need a way to declare typed, persistent state on my job classes.

*   The ActiveJob::Attributes module must be includeable in any ActiveJob::Base subclass and must add a class-level `attribute` declaration method that accepts an attribute name, a type identifier (such as :string, :integer, or :boolean), and an optional default: keyword argument.

*   Each declared attribute must generate getter and setter instance methods on the job class; if no default is specified the value must be nil, otherwise the specified default must be returned before any assignment.

*   Attribute setters must apply type casting: assigning the string '42' to an integer attribute must yield the integer 42; assigning the string '0' to a boolean attribute must yield false.

*   When a job is serialized via serialize, the returned hash must contain an 'attributes' key whose value is a hash of all declared attribute names (as strings) mapped to their current values.

*   When deserialize(data) is called with a hash containing an 'attributes' key, all recognized attribute names must be restored from that hash with proper type casting; the restored values must match the originals after a full serialize/deserialize round-trip.

*   When deserialize(data) is called with a hash containing an 'attributes' key that includes unknown attribute names not declared on the job class, deserialization must succeed without raising an error and must correctly restore the recognized attributes.

*   When deserialize(data) is called with a hash that does not contain an 'attributes' key, deserialization must succeed without raising an error and all attributes must retain their declared default values.

*   Declared attributes must persist correctly across job retries: because the job is serialized before being re-enqueued and deserialized before each attempt, any attribute mutations performed during a failed attempt must be reflected in subsequent attempts.

*   Including ActiveJob::Attributes must not break keyword argument handling in the perform method; jobs that declare attributes and also accept keyword arguments via perform must continue to receive those arguments correctly.

*   ActiveJob::Continuable jobs must also support attribute declarations; attributes must retain their accumulated values across multiple interrupt-and-resume cycles so that work resumed at a cursor position sees the correct running totals from previous executions.


*   Interface details: Type: Module
Name: ActiveJob::Attributes
Location: activejob/lib/active_job/attributes.rb
Description: An opt-in module for ActiveJob::Base subclasses that provides typed, persistent attribute declarations. When included, it adds a class-level `attribute` method and wires attribute values into the job serialization lifecycle.
Signature:
  - Class method: `attribute(name, type, **options)` where `name` is a Symbol or String, `type` is a type identifier (e.g. `:string`, `:integer`, `:boolean`), and options may include `default:` for a default value.
  - Instance reader: `<name>` — returns the current value of the attribute, type-cast.
  - Instance writer: `<name>=` — sets the attribute, applying type casting.
  - `serialize` — must include an `"attributes"` key in the returned Hash containing the current attribute values.
  - `deserialize(data)` — must restore attribute values from `data["attributes"]`; must ignore unknown attribute names silently; must use default values if the `"attributes"` key is absent from data.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.