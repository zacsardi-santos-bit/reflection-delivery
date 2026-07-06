I'm building out the label-schema feature in the MLflow experiment-tracking UI and need both the data layer and the input components to be implemented.

*   useGetLabelSchemaQuery must accept { schemaId: string } and return { isLoading: boolean, labelSchema: LabelSchema | undefined }. It must issue a GET request to /ajax-api/3.0/mlflow/label-schemas/get with 'schema_id' as a query parameter. When schemaId is an empty string, no network request must be made.

*   useGetLabelSchemaByNameQuery must accept { experimentId: string, name: string } and return { isLoading: boolean, labelSchema: LabelSchema | undefined }. It must issue a GET request to /ajax-api/3.0/mlflow/label-schemas/get-by-name with 'experiment_id' and 'name' as query parameters.

*   useListLabelSchemasQuery must accept { experimentId: string, maxResults?: number, pageToken?: string } and return { isLoading: boolean, labelSchemas: LabelSchema[], nextPageToken: string | undefined }. It must issue a GET request to /ajax-api/3.0/mlflow/label-schemas/list with 'experiment_id' always included. The 'max_results' and 'page_token' query parameters must only be included when the corresponding arguments are defined (non-undefined).

*   useCreateLabelSchemaMutation must return { createLabelSchemaAsync } where createLabelSchemaAsync sends a POST request to /ajax-api/3.0/mlflow/label-schemas/create. The full request body must include all provided fields (experiment_id, name, type, input, enable_comment) without dropping any.

*   useUpdateLabelSchemaMutation must return { updateLabelSchemaAsync } where updateLabelSchemaAsync sends a PATCH request to /ajax-api/3.0/mlflow/label-schemas/update. The update is sparse: any key in the params object with value 'undefined' must be omitted from the request body. Keys with value empty string ('') or boolean false must be included as real values and not stripped.

*   useDeleteLabelSchemaMutation must return { deleteLabelSchemaAsync } where deleteLabelSchemaAsync sends a DELETE request to /ajax-api/3.0/mlflow/label-schemas/delete. The request body must include { schema_id } identifying the schema to delete.

*   LabelSchemaInputPassFail must accept props { input: { positive_label: string, negative_label: string }, value: boolean | null, onChange: (value: boolean) => void, componentId: string }. It must render visible text for both labels. Clicking the element displaying positive_label must call onChange(true); clicking the element displaying negative_label must call onChange(false).

*   LabelSchemaInputNumeric must accept props { input: { min_value?: number, max_value?: number }, value: number | null, onChange: (value: number | null) => void, componentId: string }. It must render a number input (role spinbutton). The min and max HTML attributes must be set to input.min_value and input.max_value when provided; they must be absent (empty string) when not provided. Clearing the field must call onChange(null); entering a number must call onChange with the parsed numeric value.

*   LabelSchemaInputCategorical must accept props { input: { options: string[], multi_select?: boolean }, value: string | string[] | null, onChange: (value: string | string[]) => void, componentId: string, label: string }. It must render a combobox accessible by the label prop. When multi_select is absent or false it must be a single-select control; when multi_select is true it must be a multi-select control. Selecting an option in single-select mode must call onChange(option); selecting an option in multi-select mode from an empty selection must call onChange([option]).

*   LabelSchemaInputRenderer must accept props { input: LabelSchemaInput, value: any, onChange: (value: any) => void, componentId: string, label?: string } and dispatch to the correct widget: input.pass_fail renders the pass-fail widget; input.numeric renders a spinbutton; input.categorical renders a combobox accessible by label; input.text renders a textbox where changes call onChange with the string value. When the input object has no recognized variant, the component must render an error message that contains the text 'Invalid label schema' (case-insensitive).

*   A LabelSchema type must be defined in mlflow/server/js/src/experiment-tracking/components/label-schemas/types.ts with fields: schema_id (string), experiment_id (string), name (string), type (string), enable_comment (boolean), input (object with optional pass_fail, numeric, categorical, and text sub-fields), created_at (number), last_updated_at (number).


*   Interface details: ## Types

Type: Interface
Name: LabelSchema
Location: mlflow/server/js/src/experiment-tracking/components/label-schemas/types.ts
Description: Wire-format type for a label schema entity returned by the API.
Fields:
  schema_id: string
  experiment_id: string
  name: string
  type: string
  enable_comment: boolean
  input: LabelSchemaInput
  created_at: number
  last_updated_at: number

Type: Interface
Name: LabelSchemaInput
Location: mlflow/server/js/src/experiment-tracking/components/label-schemas/types.ts
Description: Discriminated union representing the input variant of a label schema. Exactly one field should be set on a valid schema.
Fields (all optional):
  pass_fail?: { positive_label: string; negative_label: string }
  categorical?: { options: string[]; multi_select?: boolean }
  numeric?: { min_value?: number; max_value?: number }
  text?: { max_length?: number }

---

## Hooks

Type: Function
Name: useGetLabelSchemaQuery
Location: mlflow/server/js/src/experiment-tracking/components/label-schemas/hooks/useGetLabelSchemaQuery.tsx
Signature: useGetLabelSchemaQuery({ schemaId: string }) -> { isLoading: boolean, labelSchema: LabelSchema | undefined }
Description: Issues a GET request to /ajax-api/3.0/mlflow/label-schemas/get with the schema_id query parameter. The request must not fire when schemaId is an empty string.

Type: Function
Name: useGetLabelSchemaByNameQuery
Location: mlflow/server/js/src/experiment-tracking/components/label-schemas/hooks/useGetLabelSchemaByNameQuery.tsx
Signature: useGetLabelSchemaByNameQuery({ experimentId: string, name: string }) -> { isLoading: boolean, labelSchema: LabelSchema | undefined }
Description: Issues a GET request to /ajax-api/3.0/mlflow/label-schemas/get-by-name with experiment_id and name as query parameters.

Type: Function
Name: useListLabelSchemasQuery
Location: mlflow/server/js/src/experiment-tracking/components/label-schemas/hooks/useListLabelSchemasQuery.tsx
Signature: useListLabelSchemasQuery({ experimentId: string, maxResults?: number, pageToken?: string }) -> { isLoading: boolean, labelSchemas: LabelSchema[], nextPageToken: string | undefined }
Description: Issues a GET request to /ajax-api/3.0/mlflow/label-schemas/list. The experiment_id param is always included. max_results and page_token are only included when the corresponding argument is not undefined.

Type: Function
Name: useCreateLabelSchemaMutation
Location: mlflow/server/js/src/experiment-tracking/components/label-schemas/hooks/useCreateLabelSchemaMutation.tsx
Signature: useCreateLabelSchemaMutation() -> { createLabelSchemaAsync: (params: { experiment_id: string, name: string, type: string, input: LabelSchemaInput, enable_comment?: boolean, instruction?: string }) => Promise<{ label_schema: LabelSchema }> }
Description: Issues a POST request to /ajax-api/3.0/mlflow/label-schemas/create. The entire params object is sent as the JSON body.

Type: Function
Name: useUpdateLabelSchemaMutation
Location: mlflow/server/js/src/experiment-tracking/components/label-schemas/hooks/useUpdateLabelSchemaMutation.tsx
Signature: useUpdateLabelSchemaMutation() -> { updateLabelSchemaAsync: (params: { schema_id: string, name?: string, instruction?: string, enable_comment?: boolean, input?: LabelSchemaInput }) => Promise<{ label_schema: LabelSchema }> }
Description: Issues a PATCH request to /ajax-api/3.0/mlflow/label-schemas/update. Implements sparse-update semantics: keys whose value is undefined must be stripped from the body before sending. Keys with value empty string ("") or boolean false must be forwarded as real values and must not be stripped.

Type: Function
Name: useDeleteLabelSchemaMutation
Location: mlflow/server/js/src/experiment-tracking/components/label-schemas/hooks/useDeleteLabelSchemaMutation.tsx
Signature: useDeleteLabelSchemaMutation() -> { deleteLabelSchemaAsync: (params: { schema_id: string }) => Promise<{}> }
Description: Issues a DELETE request to /ajax-api/3.0/mlflow/label-schemas/delete with { schema_id } as the JSON body.

---

## Widget Components

Type: Component
Name: LabelSchemaInputPassFail
Location: mlflow/server/js/src/experiment-tracking/components/label-schemas/widgets/LabelSchemaInputPassFail.tsx
Props: { input: { positive_label: string, negative_label: string }, value: boolean | null | undefined, onChange: (value: boolean) => void, componentId: string, disabled?: boolean }
Description: Renders a pass/fail input showing the positive_label and negative_label as clickable elements. Clicking positive_label triggers onChange(true); clicking negative_label triggers onChange(false).

Type: Component
Name: LabelSchemaInputNumeric
Location: mlflow/server/js/src/experiment-tracking/components/label-schemas/widgets/LabelSchemaInputNumeric.tsx
Props: { input: { min_value?: number, max_value?: number }, value: number | null | undefined, onChange: (value: number | null) => void, componentId: string, disabled?: boolean }
Description: Renders a number input (role="spinbutton"). The HTML min attribute is set to input.min_value and the HTML max attribute is set to input.max_value when those fields are present; both are absent (empty string) when not set. Clearing the field (empty string value) calls onChange(null); entering a valid number calls onChange with the parsed numeric value.

Type: Component
Name: LabelSchemaInputCategorical
Location: mlflow/server/js/src/experiment-tracking/components/label-schemas/widgets/LabelSchemaInputCategorical.tsx
Props: { input: { options: string[], multi_select?: boolean }, value: string | string[] | null | undefined, onChange: (value: string | string[]) => void, componentId: string, label: string, disabled?: boolean }
Description: Renders a combobox (role="combobox") accessible by the label prop. Default behavior (multi_select absent or false) is single-select; when multi_select is true it is multi-select. In single-select mode, clicking an option calls onChange(option). In multi-select mode, clicking an option when the current selection is empty calls onChange([option]).

Type: Component
Name: LabelSchemaInputRenderer
Location: mlflow/server/js/src/experiment-tracking/components/label-schemas/widgets/LabelSchemaInputRenderer.tsx
Props: { input: LabelSchemaInput, value: any, onChange: (value: any) => void, componentId: string, label?: string, disabled?: boolean, instruction?: string }
Description: Dispatcher component that renders the matching widget based on which field in input is set: input.pass_fail → LabelSchemaInputPassFail; input.numeric → LabelSchemaInputNumeric (spinbutton visible); input.categorical → LabelSchemaInputCategorical (combobox accessible by label); input.text → a textbox where changes call onChange(string). When no recognized variant is set, renders an error message containing the text "Invalid label schema" (case-insensitive match).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.