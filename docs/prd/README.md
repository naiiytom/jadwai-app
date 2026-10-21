# PRD

- `Digital_Legacy_Coverage_Gap_Vault_PRD_v1.1.1.docx`: the original. **Authoritative.**
- `PRD-v1.1.1.md`: Markdown conversion (pandoc), for grepping, linking and AI context. Single-cell
  tables in the docx (formulas, diagrams, SQL) were converted to fenced code blocks.

When a new PRD revision arrives, add the new `.docx`, regenerate the `.md` and keep the old
pair. Then update `docs/TRACEABILITY.md` and `docs/architecture/REVIEW-NOTES.md`.

```bash
pandoc PRD.docx -t gfm --wrap=none -o PRD.md
```
