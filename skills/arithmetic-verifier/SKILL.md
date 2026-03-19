# arithmetic-verifier

- **When to use:** The answer requires sums, differences, percent change, YoY growth, or unit conversion—all must be reproducible without LLM arithmetic.
- **Inputs:** Structured numeric rows from the parser; target operation (sum, ratio, growth); declared unit for each operand.
- **Procedure:**
  1. Verify all operands share compatible units (or apply explicit conversion factors from the document).
  2. Use decimal (or rational) arithmetic; record every intermediate.
  3. Compare to tolerance policy (e.g. OfficeQA 1% relative) when checking against a reference.
- **Expected outputs:** `{ "value", "unit", "trace": [ { "op", "args", "result" }, ... ] }`.
- **Confidence / failure signals:** Unit mismatch; missing operands; division by zero.
- **Fallback behavior:** Refuse to compute; surface missing provenance instead of guessing.
- **Common pitfalls:** Mixing "millions" with "thousands"; rounding before final step against spec.
