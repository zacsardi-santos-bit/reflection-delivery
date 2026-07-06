Implement a model analysis mode in the model conversion pipeline that outputs a structured description of a model's inputs and intermediate tensors when triggered by an environment variable. Ensure the analysis runs before any transformations and halts further processing after outputting the analysis.

*   Create a new module at `tools/mo/openvino/tools/mo/moc_frontend/analysis.py`:
    *   Import it as `openvino.tools.mo.moc_frontend.analysis`.
    *   Implement `json_model_analysis_dump(framework_model: Model) -> None`:
        *   Accept an OpenVINO Runtime Model.
        *   Call `json_model_analysis_print` with a dictionary containing:
            *   'inputs': Map each input parameter's friendly name to a dictionary with 'shape', 'data_type', and 'value'.
            *   'intermediate': Map each output tensor name to a dictionary with 'shape', 'data_type', and 'value'.
        *   Shape encoding:
            *   Dynamic rank: Use string "None".
            *   Static rank with dynamic dimensions: Use a list with 0 for each dynamic dimension.
            *   Fully static rank: Use a list of integers.
            *   Scalar shapes: Use an empty list `[]`.
        *   Data type encoding:
            *   Known type: Use the numpy dtype string (e.g., 'float32', 'int64').
            *   Unknown type: Use string "None".
        *   'value' field: Always use string "None".
    *   Implement `json_model_analysis_print(json_dump: dict) -> None`:
        *   Print the model analysis dictionary.
        *   Ensure this function is standalone and not nested within `json_model_analysis_dump`.

*   Modify the `moc_pipeline` function in `tools/mo/openvino/tools/mo/moc_frontend/pipeline.py`:
    *   Check if 'ANALYSIS_JSON_PRINT' is in the enabled transforms from `MO_ENABLED_TRANSFORMS`.
    *   If present:
        *   Decode the input model using `moc_front_end.decode(input_model)`.
        *   Call `json_model_analysis_dump` with the decoded model.
        *   Exit immediately using `sys.exit(0)` to skip the conversion pipeline.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.