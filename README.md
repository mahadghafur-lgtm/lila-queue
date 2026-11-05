# LILA Monday Queue

A support tool for LILA, the smart kitchen composter, that turns the Monday ticket export into a prioritised queue. It explains each ticket's priority under the four house rules and helps draft replies.

## How to open it

Double-click `index.html`. No install, server or internet needed. Works in Chrome and Safari.

## Features

- **Priority queue:** each ticket sits in a tier, with plain-English reasons naming the rule.
- **Date picker:** "today" starts at Mon Oct 5, 2026; change it and the queue re-sorts.
- **Summary bar:** replies overdue, deadlines this week, blocked, due today.
- **Patterns panel:** likely firmware issue, repeat customers, setup help, deadlines in the next 7 days.
- **Editing:** status dropdown, timestamped notes, "Mark replied" and "Reset to original data".
- **Draft reply:** template replies that follow the house rules. Unknowns are highlighted [placeholders]; Copy refuses until they are filled in.
- **Export CSV:** downloads all 12 tickets with your changes, in the same 11 columns as `tickets.csv`.

## How the tiers work

1. **Deadline about to expire:** a carrier claim (Rule 3) or refund window (Rule 2) closes within 7 days, or has passed.
2. **Customer let down:** our reply is overdue (Rule 1), or a promised date has passed.
3. **Blocked on someone else:** waiting on the warehouse, carrier, engineering or manager, or an internal step isn't done.
4. **Reply due today.**
5. **On track.**

A ticket goes in the tier of its most urgent reason. Ties go to repeat customers, then the oldest unanswered message. A customer's tickets form one case. Resolved tickets sit at the bottom.

## Assumptions

- Today is Mon Oct 5, 2026, not the real date.
- Business days are Monday to Friday; weekends are skipped.
- Maya Okafor's refund request date is her earliest ticket in the data (Sep 28). Her two earlier tickets aren't in the data, so check their dates.
- T-110 shows "owner not stated in the data"; no owner is invented.
- Drafts mentioning the software update or a downgrade start with [confirm with engineering before sending] and can't be copied until engineering confirms.

## Limitation

Edits are saved in this browser only (localStorage). They don't reach other devices or colleagues, and clearing site data removes them. Use Export CSV to keep a copy.
