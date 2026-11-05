# Build log

Short notes as we go. Each time the AI gets something wrong or we change its output, add one line. The final 200-word build notes are written from this log.

| # | What the AI did | What was wrong | How I caught it | Fix |
|---|---|---|---|---|
| 1 | Its plan said T-110 was blocked on the warehouse. | The data only says "Replacement unit not allocated yet" and names no owner. | I read the plan against the ticket notes before any code existed. | The tool shows "owner not stated in the data". |
| 2 | CLAUDE.md said the CSV has quoted fields containing commas. | None of the 12 rows do. | I rechecked every row. | Corrected the wording and kept the parser. |
| 3 | Slices 2–3 showed linked T-101 with its own "Tier 4" label inside T-109's Tier 2 case. | A case should show one tier; two tier labels in one case is confusing. | Reviewing the rendered queue after slice 3. | Slice 4: linked tickets show "Same case as T-109" and their reasons, with no separate tier label. |
| 4 | The "Data checks" line sat in the main view above the queue. | Developer check text cluttered the support person's view. | Reviewing the rendered queue after slice 3. | Slice 4: moved into a collapsed footer that opens by itself if a check fails. |
| 5 | Slice 5 Patterns panel rendered ticket links like "T-103" that wrapped across lines at the hyphen. | A ticket ID split over two lines is hard to read and click. | Claude Code found it in its own visual check of the panel. | Ticket links are now non-breaking (white-space: nowrap). |
