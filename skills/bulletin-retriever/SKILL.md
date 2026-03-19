# bulletin-retriever

- **When to use:** The question references a Treasury bulletin period (calendar month/year or fiscal year span) and/or a topic that appears in bulletin filenames or headings.
- **Inputs:** Raw question text; normalized `(year, month)` when present; topic aliases (synonyms for table titles or program names).
- **Procedure:**
  1. Read `/app/corpus/index.txt` (or repo `corpus/index.txt` in local demos).
  2. Filter candidate files by `treasury_bulletin_YYYY_MM.txt` pattern matching normalized dates.
  3. Optionally rank by lexical overlap between question tokens and file path / light grep on first N lines.
  4. On low confidence, broaden to all index entries and repeat grep-based ranking.
- **Expected outputs:** Ordered list of `{ "path": str, "rationale": str, "confidence": "high"|"medium"|"low" }`.
- **Confidence / failure signals:** No date tokens resolved; zero index matches; very low overlap with topic aliases.
- **Fallback behavior:** Strip date filter; search entire index; widen alias list.
- **Common pitfalls:** Fiscal vs calendar year off-by-one; February spelling; FY spanning two calendar years.
