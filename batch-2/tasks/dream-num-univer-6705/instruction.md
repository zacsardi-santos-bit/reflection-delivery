I'm working on the defined-name feature in a spreadsheet application.

*   The validateDefinedName function must return the string 'definedName.nameDuplicate' when the provided name already exists as a defined name in the workbook (i.e., definedNamesService.getValueByName returns a non-null result for that name).

*   The resolveDefinedNameBoxAction function must return { type: 'focusDefinedName', definedName } when inputValue case-insensitively matches an existing defined name returned by definedNamesService.getValueByName.

*   The resolveDefinedNameBoxAction function must return { type: 'focusSelection', refString: inputValue } when inputValue is a valid cell or range reference string (e.g. 'B2:C4').

*   The resolveDefinedNameBoxAction function must return { type: 'createDefinedName', name: inputValue } when inputValue is a valid new name with no conflicts (not an existing defined name, not a sheet name, not a function name, not a table name).

*   The resolveDefinedNameBoxAction function must return { type: 'reset' } when inputValue is invalid — for example, when it conflicts with a sheet name in the workbook.

*   The getAbsoluteRefStringFromSelection function must build a reference string for the active sheet. For a single cell selection (startRow === endRow and startColumn === endColumn) it must produce 'SheetName!ColRow' (e.g. 'Sheet1!A1'). For a multi-cell range it must produce 'SheetName!StartColStartRow:EndColEndRow' (e.g. 'Sheet1!B2:C4').

*   The getAbsoluteRefStringFromSelection function must call convertRefersToAbsolute with the constructed reference string, AbsoluteRefType.ALL for both row and column absolute type arguments, and the active sheet name as the fourth argument, then return the result.

*   The restoreSheetNavigationAfterDefinedNameConfirm function must always call input.blur() and must return true.

*   When the sheet cell editor is currently visible (editorBridgeService.isVisible().visible is true), restoreSheetNavigationAfterDefinedNameConfirm must call commandService.syncExecuteCommand with SetCellEditVisibleOperation.id and the payload { visible: false, eventType: DeviceInputEventType.Keyboard, keycode: KeyCode.ENTER, unitId: <unitId from editorBridgeService.getEditLocation().unitId> }.

*   When the sheet cell editor is not visible (editorBridgeService.isVisible().visible is false), restoreSheetNavigationAfterDefinedNameConfirm must call editorService.blur(true), univerInstanceService.focusUnit(unitId), editorService.getEditor()?.focus(), and set the context values FOCUSING_EDITOR_INPUT_FORMULA, EDITOR_ACTIVATED, FOCUSING_EDITOR_BUT_HIDDEN, FOCUSING_EDITOR_STANDALONE, and FOCUSING_FX_BAR_EDITOR each to false via contextService.setContextValue.


*   Interface details: Type: Function
Name: validateDefinedName
Location: packages/sheets-ui/src/views/defined-name/defined-name.utils.ts
Signature: validateDefinedName(deps: { unitId: string; name: string; workbook: Workbook; definedNamesService: { getValueByName: (unitId: string, name: string) => IDefinedNamesServiceParam | null }; superTableService: { hasTable: (name: string) => boolean }; functionService: { hasExecutor: (name: string) => boolean } }) -> string | null | undefined
Description: Validates whether a given defined name is acceptable. Returns the string 'definedName.nameDuplicate' when a defined name with the same name already exists in the workbook (i.e., definedNamesService.getValueByName returns a non-null value for that name).

Type: Function
Name: resolveDefinedNameBoxAction
Location: packages/sheets-ui/src/views/defined-name/defined-name.utils.ts
Signature: resolveDefinedNameBoxAction(deps: { unitId: string; name: string; workbook: Workbook; definedNamesService: { getValueByName: (unitId: string, name: string) => IDefinedNamesServiceParam | null }; superTableService: { hasTable: (name: string) => boolean }; functionService: { hasExecutor: (name: string) => boolean }; inputValue: string; rangeString: string }) -> { type: 'focusDefinedName'; definedName: IDefinedNamesServiceParam } | { type: 'focusSelection'; refString: string } | { type: 'createDefinedName'; name: string } | { type: 'reset' }
Description: Determines the action to take when the user presses Enter in the defined-name input box. Returns one of four action objects:
- { type: 'focusDefinedName', definedName } when inputValue case-insensitively matches an existing defined name
- { type: 'focusSelection', refString: inputValue } when inputValue is a cell or range reference (e.g. 'B2:C4')
- { type: 'createDefinedName', name: inputValue } when inputValue is a valid new defined name that does not conflict with sheet names, functions, or tables
- { type: 'reset' } when inputValue is invalid (e.g. conflicts with a sheet name)

Type: Function
Name: getAbsoluteRefStringFromSelection
Location: packages/sheets-ui/src/views/defined-name/defined-name.utils.ts
Signature: getAbsoluteRefStringFromSelection(workbook: Workbook, selections: ISelectionWithStyle[], refStringService: { convertRefersToAbsolute: (refString: string, rowAbsType: AbsoluteRefType, colAbsType: AbsoluteRefType, sheetName: string) => string }) -> string
Description: Builds an absolute reference string from the active sheet and the given selections. Uses the active sheet name and converts the range (or single cell) to a string like 'SheetName!A1' or 'SheetName!B2:C4', then calls convertRefersToAbsolute with AbsoluteRefType.ALL for both row and column absolute types and the sheet name, returning the result.

Type: Function
Name: restoreSheetNavigationAfterDefinedNameConfirm
Location: packages/sheets-ui/src/views/defined-name/defined-name.utils.ts
Signature: restoreSheetNavigationAfterDefinedNameConfirm(deps: { unitId: string; input: { blur: () => void }; commandService: { syncExecuteCommand: (id: string, params: object) => boolean }; univerInstanceService: { focusUnit: (unitId: string) => void }; editorService: { blur: (force?: boolean) => void; getEditor: () => { focus: () => void } | null | undefined }; editorBridgeService: { isVisible: () => { visible: boolean }; getEditLocation: () => { unitId: string } | null }; contextService: { setContextValue: (key: string, value: boolean) => void } }) -> boolean
Description: Restores keyboard focus to the sheet grid after the user confirms a defined-name action. Always calls input.blur(). If the sheet cell editor is currently visible (editorBridgeService.isVisible().visible === true), closes it by calling commandService.syncExecuteCommand with SetCellEditVisibleOperation.id and { visible: false, eventType: DeviceInputEventType.Keyboard, keycode: KeyCode.ENTER, unitId: <editingUnitId from getEditLocation> }. If the editor is not visible, calls editorService.blur(true), univerInstanceService.focusUnit(unitId), editorService.getEditor()?.focus(), and sets FOCUSING_EDITOR_INPUT_FORMULA, EDITOR_ACTIVATED, FOCUSING_EDITOR_BUT_HIDDEN, FOCUSING_EDITOR_STANDALONE, and FOCUSING_FX_BAR_EDITOR all to false via contextService.setContextValue. Always returns true.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.