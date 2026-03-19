# plaintext-table-parser

- **When to use:** Evidence lives in fixed-width or plaintext tables inside `.txt` bulletin extracts; answers depend on numeric columns with explicit units in headers or captions.
- **Inputs:** File path(s); optional table anchor string (e.g. caption fragment); question hints for column names.
- **Procedure:**
  1. Locate table block (caption, header row, dashed rule, data rows).
  2. Split rows into columns using fixed widths or consistent delimiters; preserve line numbers.
  3. Normalize numbers: strip commas; interpret `(123)` as negative; skip "Total"/subtotal rows when instructed.
  4. Attach unit annotations from header/caption (e.g. "Millions of dollars").
- **Expected outputs:** JSON-serializable rows: `{ "line_start", "line_end", "label", "columns": {...}, "unit" }`.
- **Confidence / failure signals:** Ambiguous alignment; multiple tables with same caption; missing unit line.
- **Fallback behavior:** Try alternate delimiter heuristics; return top-k candidate spans for human/agent review.
- **Common pitfalls:** Wrapped continuation lines; OCR spacing; confusing subtotals with data rows.
