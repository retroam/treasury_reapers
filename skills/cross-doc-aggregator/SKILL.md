# cross-doc-aggregator

- **When to use:** Questions require consolidating figures across multiple months, quarters, or years before applying arithmetic.
- **Inputs:** Ranked file list from bulletin-retriever; per-file structured tables from plaintext-table-parser; aggregation spec (e.g. sum over FY months).
- **Procedure:**
  1. Iterate candidates in priority order; parse each into normalized records keyed by `(period, series_id)`.
  2. Merge into one dataset; detect duplicates and subtotal rows.
  3. Pass merged operands to arithmetic-verifier.
- **Expected outputs:** Consolidated dataset JSON + list of contributing files and line spans.
- **Confidence / failure signals:** Incomplete period coverage; conflicting duplicates; missing months in FY window.
- **Fallback behavior:** Widen retrieval; mark coverage gaps explicitly in the answer payload.
- **Common pitfalls:** Double-counting totals printed in each monthly table; fiscal-year boundaries.
