I'm working on adding admin controls for the Concourse wall broadcast feature.

*   A button for managing the wall broadcast message must only be rendered in the UI for users who are logged in AND have admin privileges. It must not appear when no user is logged in, and it must not appear for authenticated non-admin users. This button must render with the HTML element id 'set-wall-button'.

*   A wall editor panel (containing an input element with HTML id 'wall-editor-message') must not be visible by default on initial load.

*   Clicking SetWallButton must open the wall editor panel (making the element with id 'wall-editor-message' appear). Clicking SetWallButton again while the editor is open must close it (toggle behavior).

*   Clicking CloseWallEditorButton must close the wall editor panel.

*   Navigating away from the dashboard to any other route must automatically close the wall editor panel.

*   A successful logout (LoggedOut callback with Ok result) must close the wall editor panel.

*   After typing a message via EditWallMessage and clicking SaveWallButton, the application must produce a DoSetWall effect with a record containing the entered message string and ttl equal to 0.

*   Clicking ClearWallButton must produce a DoClearWall effect and close the wall editor panel.

*   When a WallSet callback arrives with a successful result (Ok), the wall editor must close and a FetchWall effect must be produced.

*   When a WallSet callback arrives with an error result (Err), the wall editor must remain open.

*   When a WallCleared callback arrives with a successful result (Ok), the wall banner element (id 'wall-banner') must be removed from the view.


*   Interface details: Type: Variant
Name: SetWallButton
Location: web/elm/src/Message/Message.elm
Signature: SetWallButton (variant of type DomID)
Description: DomID variant for the button that opens/closes the wall editor. The toHtmlID function in web/elm/src/Message/Effects.elm must map this DomID to the string "set-wall-button", producing an HTML element with that id. This element must only be rendered for logged-in admin users.

Type: Variant
Name: CloseWallEditorButton
Location: web/elm/src/Message/Message.elm
Signature: CloseWallEditorButton (variant of type DomID)
Description: DomID variant for the close button inside the wall editor.

Type: Variant
Name: SaveWallButton
Location: web/elm/src/Message/Message.elm
Signature: SaveWallButton (variant of type DomID)
Description: DomID variant for the save/submit button inside the wall editor.

Type: Variant
Name: ClearWallButton
Location: web/elm/src/Message/Message.elm
Signature: ClearWallButton (variant of type DomID)
Description: DomID variant for the clear-wall button inside the wall editor.

Type: Variant
Name: EditWallMessage
Location: web/elm/src/Message/Message.elm
Signature: EditWallMessage String (variant of type Message)
Description: Message variant carrying the current text entered by the user in the wall editor input field.

Type: Variant
Name: DoSetWall
Location: web/elm/src/Message/Effects.elm
Signature: DoSetWall { message : String, ttl : Int } (variant of type Effect)
Description: Effect variant that instructs the runtime to persist a new wall broadcast message. The record must have a "message" field (String) and a "ttl" field (Int). When SaveWallButton is clicked after editing, this effect is produced with the entered message and ttl = 0.

Type: Variant
Name: DoClearWall
Location: web/elm/src/Message/Effects.elm
Signature: DoClearWall (variant of type Effect)
Description: Effect variant that instructs the runtime to delete the current wall broadcast message. Produced when ClearWallButton is clicked.

Type: Variant
Name: WallSet
Location: web/elm/src/Message/Callback.elm
Signature: WallSet (Result Http.Error ()) (variant of type Callback)
Description: Callback variant delivered when the API call to set the wall message completes. Ok () closes the editor and produces a FetchWall effect; Err _ keeps the editor open.

Type: Variant
Name: WallCleared
Location: web/elm/src/Message/Callback.elm
Signature: WallCleared (Result Http.Error ()) (variant of type Callback)
Description: Callback variant delivered when the API call to clear the wall message completes. Ok () removes the wall banner (element id "wall-banner") from the view.

Note: The wall editor panel must render an input element with HTML id "wall-editor-message". In the reference implementation this id is produced by a DomID variant called WallEditorTextarea, mapped to "wall-editor-message" by the toHtmlID function in web/elm/src/Message/Effects.elm. Any implementation that produces an element with id "wall-editor-message" is acceptable.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.