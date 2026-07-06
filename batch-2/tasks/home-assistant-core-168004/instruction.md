I'm working with the Elgato integration in Home Assistant and I've noticed that the error messages are both inaccurate and unhelpful.

*   When a connection error (ElgatoConnectionError) is raised during any Elgato entity operation (button press, light control, switch control), the integration must raise a HomeAssistantError whose message contains exactly: 'An error occurred while communicating with the Elgato device'

*   When a generic/unknown error (ElgatoError but not ElgatoConnectionError) is raised during any Elgato entity operation (button press, light control, switch control, identify), the integration must raise a HomeAssistantError whose message contains exactly: 'An unknown error occurred while communicating with the Elgato device'

*   Connection errors (ElgatoConnectionError) and generic errors (ElgatoError) must be handled separately and produce distinct error messages — the connection error message does not include 'unknown', while the generic error message includes 'unknown'

*   The button entity must handle both ElgatoConnectionError and ElgatoError when a button is pressed, raising the appropriate HomeAssistantError for each

*   The light entity must handle ElgatoConnectionError when controlling the light (turning on or off), raising a HomeAssistantError with the connection error message

*   The light entity must handle ElgatoError when identifying the light, raising a HomeAssistantError with the unknown error message

*   The switch entity must handle ElgatoError when turning the switch on or off, raising a HomeAssistantError with the unknown error message

*   Error messages must refer to 'Elgato device' (not 'Elgato Light') across all entity types including buttons, lights, and switches


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.