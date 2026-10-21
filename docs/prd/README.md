# PRD

- `PRD-v1.1.1.md`: Markdown conversion of *Digital Legacy & Coverage Gap Vault PRD v1.1.1*
  (pandoc), used for grepping, linking and AI context. Single-cell tables in the original
  (formulas, diagrams, SQL) were converted to fenced code blocks.
- The original `.docx` is **not committed for now**. It stays with the product owner, and
  `docs/prd/*.docx` is git-ignored. If the two disagree, the `.docx` wins, so raise the mismatch.

When a new PRD revision arrives, regenerate the `.md` from the new `.docx`, keep the old `.md`,
and update `docs/TRACEABILITY.md` and `docs/architecture/REVIEW-NOTES.md`.

```bash
pandoc PRD.docx -t gfm --wrap=none -o PRD-vX.Y.Z.md
```
