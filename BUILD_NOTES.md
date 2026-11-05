# Build log

Short notes as we go. Each time the AI gets something wrong or we change its output, add one line. The final 200-word build notes are written from this log.

| # | What the AI did | What was wrong | How I caught it | Fix |
|---|---|---|---|---|
| 1 | Its plan said T-110 was blocked on the warehouse. | The data only says "Replacement unit not allocated yet" and names no owner. | I read the plan against the ticket notes before any code existed. | The tool shows "owner not stated in the data". |
| 2 | CLAUDE.md said the CSV has quoted fields containing commas. | None of the 12 rows do. | I rechecked every row. | Corrected the wording and kept the parser. |
