## Description

It would be useful to support placing annotation-style operations — operations that target no qubits — in circuit moments and have them display visibly in the circuit's text diagram. Currently, zero-qubit operations placed inside moments are silently ignored when generating the text diagram, making it impossible to attach textual labels or markers to specific points in a circuit.

## Expected Behavior

- When a moment contains one or more operations that target no qubits, those operations should appear as text labels below the qubit wire rows in the text diagram, in the column corresponding to their moment.
- If multiple zero-qubit annotations appear in the same moment, they should stack vertically below the diagram, one per line, in the order they appear in the moment.
- If the circuit also has a global phase marker, that appears on the first row below the qubit wires, and custom annotations should follow on subsequent rows.
- If a zero-qubit operation does not provide explicit diagram information, its default string representation should be used as the label.
- Zero-qubit gates (applied with no qubit arguments) should be treated the same way as zero-qubit operations.
- If a circuit operation wrapping a subcircuit contains zero-qubit annotations internally, those annotations should be surfaced in the outer circuit's diagram at the appropriate column.

## Why This Matters

This allows users to annotate specific moments in a circuit diagram with custom labels — useful for documentation, debugging, and communication — without cluttering the qubit wire rows. Having annotations appear below the diagram in the correct column makes circuit structure immediately visible in the text output.
