# Crash Driver Examples

This contains the Crash Driver Message Spec used in NIEM Training to work out mappings and issues in transformations between XML Schema, Common Model Format (CMF), and JSON Schema.

The XML Schema files were generated from the SSGT, although the cardinality has been tightened up by hand.

The CMF was generated from the XML Schema files using Dr. Scott Renner's transformation tool, embedded in the NIEM API being developed by GTRI. Until licensing issues are resolved, the code cannot yet be contributed.

The JSON Schema was hand-developed as an exercise in working out how to represent various cardinality constraints in JSON Schema. It's a work in progress.

The sample XML file should validate against the XML Schema files.

The sample JSON-LD file should validate against the JSON Schema.

The sample CMF should validate against the schema for CMF 0.5, but that schema is not included here.
