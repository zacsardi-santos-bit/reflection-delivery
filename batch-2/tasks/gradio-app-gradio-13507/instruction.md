I've been trying to load an external JavaScript library through the Gradio HTML component by including a script tag in the HTML content I pass to it.

*   When the gr.HTML component is instantiated with a string value containing a '<script>' tag, it must issue a Python UserWarning.

*   The UserWarning message must contain the string '<script>'.

*   When gr.HTML is instantiated with plain HTML that contains no '<script>' tags, no warnings of any kind should be raised.


*   Interface details: Type: Class
Name: HTML
Location: gradio/components/html.py
Description: The existing HTML component class. Its initialization must be modified so that when the value passed to the component contains a '<script>' tag, a UserWarning is issued. The warning message must include the string '<script>'. No warning should be issued when the value does not contain a '<script>' tag.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.