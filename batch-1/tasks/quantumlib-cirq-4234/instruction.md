Implement support for zero-qubit operations in circuit moments to appear as annotations in text circuit diagrams. Ensure these operations are visible below the qubit wire rows, aligned with their respective moments.

*   Display zero-qubit operations as text annotations below all qubit wire rows in the text diagram, aligned with the moment's column.
    *   Exclude `GlobalPhaseOperation` from this behavior.
*   Stack multiple zero-qubit annotations vertically within a single moment, maintaining their order of appearance.
*   Position the 'global phase:' row first below the qubit rows if a `GlobalPhaseOperation` is present, with custom zero-qubit annotations following on subsequent rows.
*   Use the string representation of a zero-qubit operation as the annotation text if it does not implement the circuit diagram info protocol.
*   Treat zero-qubit gates (gates with a qubit count of 0) applied with no qubit arguments as annotations, similar to zero-qubit operations.
*   Extract and display internal zero-qubit annotations from nested frozen circuits in the outer circuit's text diagram, aligned with the corresponding outer moment's column.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.